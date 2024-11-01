## `parseJson`

### 签名

```solidity
// Return the value(s) that correspond to 'key'
vm.parseJson(string memory json, string memory key)
// Return the entire JSON file
vm.parseJson(string memory json);
```

### 描述

这些作弊码用于解析字符串形式的 JSON 文件。通常，它与 `vm.readFile()` 配合使用，后者以字符串形式返回整个文件。

您可以使用 `forge-std` 中的 `stdJson` 作为辅助库，以获得更好的用户体验。

该作弊码接受一个 `key`，用于在 JSON 中搜索特定值，或者不带 `key` 以返回整个 JSON。它将值作为 abi 编码的 `bytes` 数组返回。这意味着您必须使用 `abi.decode()` 将其解码为适当的类型，否则它将会 `revert`。

### JSONpath Key

`parseJson` 使用一种称为 JSONpath 的语法来为任意的 JSON 文件形成任意的键。相同的语法（或者说是一种方言）被工具 [`jq`](https://stedolan.github.io/jq/) 使用。

要了解更多关于该语法的信息，您可以访问我们在内部使用的 rust 库的 [README](https://crates.io/crates/jsonpath-rust)。这样您就可以确保您正在使用正确的 jsonPath 方言。

### JSON 编码规则

我们使用 `number`、`string`、`object`、`array`、`boolean`、`null` 这些术语，因为它们在 [JSON 规范](https://www.w3schools.com/js/js_json_datatypes.asp)中有定义。

**编码规则**

- `null` 被编码为 `bytes32(0)`
- 大于等于 0 的数字被编码为 `uint256`
- 负数被编码为 `int256`
- 不允许使用带小数位的浮点数。
- 使用科学计数法的浮点数可以是 `uint256` 或 `int256`，具体取决于值。
- 可以解码为 `H160` 类型并以 `0x` 开头的字符串被编码为 `address`。换句话说，如果它可以解码为地址，那么它很可能是一个地址
- 以 `0x` 开头的字符串，如果长度为 `66`，则被编码为 `bytes32`，否则编码为 `bytes`
- 既不是 `address`、`bytes32` 或 `bytes` 的字符串被编码为 `string`
- 数组被编码为其第一个元素类型的动态数组
- 对象（`{}`）被编码为 `tuple`

### 类型强制转换

如上所述，`parseJSON` 需要推断 JSON 值的类型，这具有一些固有的限制。因此，有一个 `parseJson*` 作弊码子系，用于强制转换返回值的类型。

例如，`vm.parseJsonUint(json, key)` 将强制将值转换为 `uint256`。这意味着它可以解析以下所有值并将它们作为 `uint256` 返回。这包括类型为 `number` 的数字，作为 `string` 的数字化值，当然还包括其十六进制表示。

```json
{
  "hexUint": "0x12C980",
  "stringUint": "115792089237316195423570985008687907853269984665640564039457584007913129639935",
  "numberUint": 115792089237316195423570985008687907853269984665640564039457584007913129639935
}
```

类似地，对于所有类型（包括 `bytes` 和 `bytes32`）以及它们的数组（`vm.parseJsonUintArray`）都有作弊码。

### 将 JSON 对象解码为 Solidity 结构体

JSON 对象被编码为元组，可以通过元组或结构体进行解码。这意味着您可以在 Solidity 中定义一个 `struct`，并将整个 JSON 对象解码为该 `struct`。

例如：

以下 JSON：

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

由于值被作为 abi 编码的元组返回，因此结构体的属性名称不需要与 JSON 中的键名匹配。上述 json 文件也可以解码为：

```solidity
struct Json {
    uint256 apple;
    string pineapple;
}
```

重要的是字母顺序。由于 JSON 对象是一个无序的数据结构，但元组是一个有序的数据结构，我们必须以某种方式给 JSON 排序。最简单的方法是按照键的字母顺序进行排序。这意味着为了正确解码 JSON 对象，您需要定义结构体的属性，这些属性的 **类型** 对应于 JSON 键的字母顺序的值。

- 结构体是按顺序解释的。这意味着元组的第一个项将根据结构体定义的第一个项进行解码（不是按字母顺序）。
- JSON 将按字母顺序解析，而不是按顺序解析。
- 请注意，此解析在底层使用了 Rust 的 BTreeMap crate，这意味着大写和小写字符串会被区别对待。在这种词典排序中，大写字符*优先于*小写字符，即"Zebra"会优先于"apple"。

因此，JSON 的第一个（按字母顺序）值将被 abi 编码，然后尝试根据 `struct` 的第一个属性的类型进行 abi 解码。

上述 JSON 将无法使用以下结构体进行解码：

```solidity
struct Json {
    uint256 b;
    uint256 a;
}
```

原因是它会尝试将字符串 `"sigma"` 解码为 uint。确切地说，它会被解码，但会得到一个错误的数字，因为它会错误地解释字节。

另一个例子，给定以下 JSON：

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

And Solidity structs defined as follows:

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

One would decode the JSON as follows:

```solidity
string memory root = vm.projectRoot();
string memory path = string.concat(root, "/src/test/fixtures/fruitstall.json");
string memory json = vm.readFile(path);
bytes memory data = vm.parseJson(json);
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

### 解码 JSON 对象的提示

如果您的 JSON 对象中有 `十六进制数`，它们将被编码为 `bytes`。为了更好的用户体验，将它们解码为 `uint` 的方法是定义两个 `struct`，一个中间 `struct` 定义这些值为 `bytes`，然后一个最终 `struct` 供用户使用。

1. 将 JSON 解码为中间 `struct`
2. 将中间 `struct` 转换为最终 `struct`，将 `bytes` 转换为 `uint`。我们在 `forge-std` 中有一个辅助函数来执行此操作
3. 将最终的 `struct` 提供给用户使用

### 如何使用 StdJson

1. 导入库 `import {stdJson} from "forge-std/StdJson.sol";`
2. 使用 `string` 定义其用法：`using stdJson for string;`
3. 如果要解析简单值（数字、地址等），请使用辅助函数
4. 如果要解析整个 JSON 对象：
   1. 在 Solidity 中定义 `struct`。确保按字母顺序--这很难调试
   2. 使用 `parseRaw()` 辅助函数返回 abi 编码的 `bytes`，然后解码为您的 `struct`

```solidity
string memory root = vm.projectRoot();
string memory path = string.concat(root, "/src/test/fixtures/broadcast.log.json");
string memory json = vm.readFile(path);
bytes memory transactionDetails = json.parseRaw(".transactions[0].tx");
RawTx1559Detail memory rawTxDetail = abi.decode(transactionDetails, (RawTx1559Detail));
```

### Forge 脚本工件

我们已经创建了一些辅助结构和函数，用于读取广播 Forge 脚本生成的工件。

目前，我们仅支持 EIP1559 兼容链生成的工件，并且我们 **尚不** 支持解析整个 `broadcast.json` 工件。您需要解析单独的值，例如 `transactions`、`receipts` 等。

要读取交易，只需执行以下操作：

```solidity
function testReadEIP1559Transactions() public {
    string memory root = vm.projectRoot();
    string memory path = string.concat(root, "/src/test/fixtures/broadcast.log.json");
    Tx1559[] memory transactions = readTx1559s(path);
}
```

然后您可以在这些结构中访问它们的各种字段：

```solidity
struct Tx1559 {
    string[] arguments;
    address contractAddress;
    string contractName;
    string functionSig;
    bytes32 hash;
    Tx1559Detail txDetail;
    string opcode;
}

struct Tx1559Detail {
    AccessList[] accessList;
    bytes data;
    address from;
    uint256 gas;
    uint256 nonce;
    address to;
    uint256 txType;
    uint256 value;
}
```

### 故障排除

#### 无法读取文件

> 失败。原因：路径 `<file-path>` 不允许进行读取操作

如果收到此错误，请确保您在 `foundry.toml` 中使用 [`fs_permissions`键](./fs.md) 启用了读取权限。

### 参考

- 辅助库：[stdJson.sol](https://github.com/foundry-rs/forge-std/blob/master/src/StdJson.sol)
- 使用示例：[stdCheats.t.sol](https://github.com/foundry-rs/forge-std/blob/ca8d6e00ea9cb035f6856ff732203c9a3c48b966/src/test/StdCheats.t.sol#L206)
- [文件作弊码](./fs.md)：处理文件的作弊码