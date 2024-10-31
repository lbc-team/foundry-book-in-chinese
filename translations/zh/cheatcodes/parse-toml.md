## `parseToml`

### 签名

```solidity
// Return the value(s) that correspond to 'key'
vm.parseToml(string memory toml, string memory key)
// Return the entire TOML file
vm.parseToml(string memory toml);
```

### 描述

这些作弊码用于在转换为 JSON 后解析字符串形式的 TOML 文件。通常，它与 `vm.readFile()` 结合使用，该函数以字符串形式返回整个文件。

您可以使用 `forge-std` 中的 `stdToml` 作为辅助库，以获得更好的用户体验。

该作弊码接受一个 `key` 来搜索 TOML 中的特定值，或者不提供 key 来返回整个 TOML。它将值作为 abi 编码的 `bytes` 数组返回。这意味着您必须使用 `abi.decode()` 将其解码为适当的类型，以便其正常工作，否则将会 `revert`。

### JSONpath 键

`parseToml` 使用一种称为 JSONpath 的语法来形成任意 JSON 文件的任意键。该语法（或更确切地说是一种方言）也被工具 [`jq`](https://stedolan.github.io/jq/) 使用。

要了解更多关于该语法的信息，您可以访问我们在底层实现该功能时使用的 Rust 库的 [README](https://crates.io/crates/jsonpath-rust)。这样您可以确保使用的是正确的 jsonPath 方言。

### 编码规则

我们使用术语 `string`、`integer`、`float`、`boolean`、`array`、`datetime`、`inline-table`，这些术语在 [TOML 规范](https://www.w3schools.io/file/toml-datatypes/) 中有定义。

我们使用术语 `number`、`string`、`object`、`array`、`boolean`、`null`，这些术语在 [JSON 规范](https://www.w3schools.com/js/js_json_datatypes.asp) 中有定义。

**TOML 编码规则**

- `float` 限制为 32 位（即 `+1.5`）。建议使用字符串以防止精度丢失
- `integer` 限制为 64 位（即 `9223372036854775807`）。建议使用字符串来编码大值
- 数组值不能混合类型（即 `[256, "b"]`，只能是 `[256, 512]` 或 `["a", "b"]`）
- `datetime` 在转换时编码为 `string`
- `float` 在转换时编码为 `number`
- `integer` 在转换时编码为 `number`
- `inline-table`（或 `table`）在转换时编码为 `object`
- `null` 编码为 `"null"` 字符串

**JSON 编码规则**

- `null` 编码为 `bytes32(0)` 或 `""`
- 大于等于 0 的数字编码为 `uint256`
- 负数编码为 `int256`
- 不允许带小数位的浮点数
- 使用科学计数法的浮点数可以是 `uint256` 或 `int256`，具体取决于值
- 可以解码为 `H160` 类型并以 `0x` 开头的字符串编码为 `address`。换句话说，如果它可以解码为地址，它可能就是一个地址
- 以 `0x` 开头的字符串，如果长度为 `66`，则编码为 `bytes32`，否则编码为 `bytes`
- 既不是 `address`、`bytes32` 也不是 `bytes` 的字符串编码为 `string`
- 数组编码为其第一个元素类型的动态数组
- 对象 (`{}`) 编码为 `tuple`

### 类型强制转换

如上所述，`parseToml` 需要推断 TOML 值的类型，这有一些固有的限制。因此，有一组 `parseToml*` 作弊码可以强制转换返回值的类型。

例如，`vm.parseTomlUint(toml, key)` 将值强制转换为 `uint256`。这意味着它可以解析以下所有值并将其返回为 `uint256`。这包括类型为 `number` 的数字、作为 `string` 的字符串化数字，当然还有它的十六进制表示。

```toml
hexUint = "0x12C980"
stringUint = "115792089237316195423570985008687907853269984665640564039457584007913129639935"
numberUint = 9223372036854775807 # TOML is limited to 64-bit integers
```

类似地，所有类型（包括 `bytes` 和 `bytes32`）及其数组都有作弊码（`vm.parseTomlUintArray`）。

### 将 TOML 表解码为 Solidity 结构体

TOML 表被转换为 JSON 对象。JSON 对象被编码为元组，可以通过元组或结构体进行解码。这意味着您可以在 Solidity 中定义一个 `struct`，它将把整个 JSON 对象解码到该 `struct` 中。

例如：

以下 TOML：

```toml
a = 43
b = "sigma"
```

将被转换为以下 JSON：

```json
{
  "a": 43,
  "b": "sigma"
}
```

将被解码为：

```solidity
struct Json {
    uint256 a;
    string b;
}
```

由于值作为 abi 编码的元组返回，因此结构体的属性名称不需要与 JSON 中的键名称匹配。上述 JSON 文件也可以解码为：

```solidity
struct Json {
    uint256 apple;
    string pineapple;
}
```

重要的是字母顺序。由于 JSON 是一种无序数据结构，而元组是有序的，因此我们必须以某种方式给 JSON 赋予顺序。最简单的方法是按字母顺序对键进行排序。这意味着为了正确解码 JSON 对象，您需要定义结构体的属性，其 **类型** 与 JSON 键的字母顺序对应。

- 结构体是串行解释的。这意味着元组的第一个项目将根据结构体定义的第一个项目进行解码（没有字母顺序）。
- JSON 将按字母顺序解析，而不是串行。
- 请注意，这种解析在底层使用 Rust 的 BTreeMap crate，这意味着大写和小写字符串被视为不同。大写字符 *优先* 于小写字符在字典序中，即 "Zebra" 会优先于 "apple"。

因此，JSON 中的第一个（按字母顺序）值将被 abi 编码，然后尝试根据 `struct` 的第一个属性的类型进行 abi 解码。

上述 TOML 将无法使用以下结构体解码：

```solidity
struct Json {
    uint256 b;
    uint256 a;
}
```

原因是它会尝试将字符串 `"sigma"` 解码为 uint。确切地说，它会被解码，但会导致错误的数字，因为它会错误地解释字节。

另一个例子，给定以下 TOML：

```toml
name = "Fresh Fruit"

[[apples]]
sweetness = 7
sourness = 3
color = "Red"

[[apples]]
sweetness = 5
sourness = 5
color = "Green"

[[apples]]
sweetness = 9
sourness = 1
color = "Yellow"
```

将被转换为以下 JSON：

```json
{
    "apples": [
        {
            "sweetness": 7,
            "sourness": 3,
            "color": "Red"
        },
        {
            "sweetness": 5,
            "sourness": 5,
            "color": "Green"
        },
        {
            "sweetness": 9,
            "sourness": 1,
            "color": "Yellow"
        }
    ],
    "name": "Fresh Fruit"
}
```

并且 Solidity 结构体定义如下：

```solidity
struct Apple {
    string color;
    uint8 sourness;
    uint8 sweetness;
}

struct FruitStall {
    Apple[] apples;
    string name;
}
```

可以按如下方式解码 TOML：

```solidity
string memory root = vm.projectRoot();
string memory path = string.concat(root, "/src/test/fixtures/fruitstall.toml");
string memory toml = vm.readFile(path);
bytes memory data = vm.parseToml(toml);
FruitStall memory fruitstall = abi.decode(data, (FruitStall));

// Logs: Welcome to Fresh Fruit
console2.log("Welcome to", fruitstall.name);

for (uint256 i = 0; i < fruitstall.apples.length; i++) {
    Apple memory apple = fruitstall.apples[i];

    // Logs:
    // Color: Red, Sourness: 3, Sweetness: 7
    // Color: Green, Sourness: 5, Sweetness: 5
    // Color: Yellow, Sourness: 1, Sweetness: 9
    console2.log(
        "Color: %s, Sourness: %d, Sweetness: %d",
        apple.color,
        apple.sourness,
        apple.sweetness
    );
}
```

### 如何使用 StdToml

1. 导入库 `import {stdToml} from "forge-std/StdToml.sol";`
2. 使用 `string` 定义其用法：`using stdToml for string;`
3. 如果您想解析简单值（数字、地址等），请使用辅助函数
4. 如果您想解析整个 TOML 表：
   1. 在 Solidity 中定义 `struct`。确保遵循字母顺序——这很难调试
   2. 使用 `parseRaw()` 辅助函数返回 abi 编码的 `bytes`，然后将其解码为您的结构体

```solidity
string memory root = vm.projectRoot();
string memory path = string.concat(root, "/src/test/fixtures/config.toml");
string memory toml = vm.readFile(path);
bytes memory data = toml.parseRaw(".");
Config memory config = abi.decode(data, (Config))
```

### 故障排除

#### 无法读取文件

> FAIL. 原因：路径 `<file-path>` 不允许进行读取操作

如果您收到此错误，请确保在 `foundry.toml` 中使用 [`fs_permissions` 键](./fs.md) 启用读取权限。

### 参考

- 辅助库：[stdToml.sol](https://github.com/foundry-rs/forge-std/blob/master/src/StdToml.sol)
- [文件作弊码](./fs.md)：用于处理文件的作弊码