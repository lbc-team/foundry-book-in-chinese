## `writeToml`

### 签名

```solidity
function writeToml(string calldata json, string calldata path) external;

function writeToml(string calldata json, string calldata path, string calldata valueKey) external;
```

### 描述

在转换后将序列化的 JSON 对象写入 TOML 文件。

参数 `json` 必须是字符串化的 JSON 对象。例如：

```text
{ "boolean": true, "number": 342, "object": { "title": "finally json serialization" } }
```

这通常通过 [serializeJson](./serialize-json.md) 构建。

参数 `path` 是要写入的 TOML 文件的路径。

如果未提供 `valueKey`，则 TOML 对象将写入新文件。如果文件已存在，将被覆盖。

如果提供了 `valueKey`，则文件必须已经存在并且是有效的 TOML 文件。该文件中的对象将通过在 TOML 转换后用 JSON 对象 `json` 替换 *JSON 路径* `valueKey` 的值进行更新。

这对于在不必先解析然后重新序列化的情况下替换 TOML 文件中的某些值非常有用。请注意，TOML 路径必须指示一个现有的键，因此无法通过这种方式添加新键。

**请记住：** 文件路径 `path` 需要在允许的路径中。详细信息请参阅 [File cheatcodes](./fs.md)。

#### JSON 路径

让我们考虑以下 JSON 对象：

```json
{
  "boolean": true,
  "number": 342,
  "obj1": {
    "aNumber": 123,
    "obj2": {
      "aNumber": 123,
      "obj3": {
        "veryDeep": 13371337
      }
    }
  }
}
```

根对象总是被假定，因此我们可以通过以点 (`.`) 开头的路径引用其子对象。例如，`.boolean`、`.number` 和 `.obj1`。
我们可以深入到任意层级：`.obj1.aNumber` 或 `.obj1.obj2.aNumber`。
我们甚至可以在子树中搜索一个键：`.obj1..veryDeep`，或者仅使用 `..veryDeep`，因为没有歧义。

请查看示例以了解其实际应用。

### 示例

#### 一个简单的例子

```solidity
string memory jsonObj = '{ "boolean": true, "number": 342, "myObject": { "title": "finally json serialization" } }';
vm.writeToml(jsonObj, "./output/example.toml");

// replaces the value of `myObject` with a new object
string memory newJsonObj = '{ "aNumber": 123, "aString": "asd" }';
vm.writeToml(newJsonObj, "./output/example.toml", ".myObject");

// replaces the value of `aString` in the new object
vm.writeToml("my new string", "./output/example.toml", ".myObject.aString");

// Here's example.toml:
// 
// boolean = true
// number = 342

// [myObject]
// aNumber = 123
// aString = "my new string"
```

#### 一个更复杂的例子

```solidity
string memory jsonObj = '{ "boolean": true, "number": 342, "obj1": { "foo": "bar" } }';
vm.writeToml(jsonObj, "./output/example2.toml");

string memory jsonObj2 = '{ "aNumber": 123, "obj2": {} }';
vm.writeToml(jsonObj2, "./output/example2.toml", ".obj1");

string memory jsonObj3 = '{ "aNumber": 123, "obj3": { "veryDeep": 3 } }';
vm.writeToml(jsonObj3, "./output/example2.toml", ".obj1.obj2");

// Here's example2.toml so far:
//
// boolean = true
// number = 342
//
// [obj1]
// aNumber = 123
//
// [obj1.obj2]
// aNumber = 123
//
// [obj1.obj2.obj3]
// veryDeep = 3

// Note that the JSON object is just the value 13371337 in this case.
vm.writeToml("13371337", "./output/example2.toml", "..veryDeep");

// Here's the final example2.toml:
//
// boolean = true
// number = 342
//
// [obj1]
// aNumber = 123
//
// [obj1.obj2]
// aNumber = 123
//
// [obj1.obj2.obj3]
// veryDeep = 13371337
```

### 另请参阅

- [serializeJson](./serialize-json.md)