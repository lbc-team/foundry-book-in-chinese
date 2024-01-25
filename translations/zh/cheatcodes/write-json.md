## `writeJson`

### 签名

```solidity
function writeJson(string calldata json, string calldata path) external;

function writeJson(string calldata json, string calldata path, string calldata valueKey) external;
```

### 描述

将序列化的 JSON 对象写入文件。

参数 `json` 必须是字符串化形式的 JSON 对象。例如：

```text
{ "boolean": true, "number": 342, "object": { "title": "finally json serialization" } }
```

通常是通过 [serializeJson](./serialize-json.md) 构建的。

参数 `path` 是要写入的 JSON 文件的路径。

如果未提供 `valueKey`，则将 JSON 对象写入新文件。如果文件已存在，则将被覆盖。

如果提供了 `valueKey`，则文件必须已经存在并且是有效的 JSON 文件。该文件中的对象将通过用 JSON 对象 `json` 替换 *JSON 路径* `valueKey` 处的值来进行更新。

这对于替换 JSON 文件中的某些值而无需首先解析然后重新序列化它非常有用。请注意，JSON 路径必须指示一个现有的键，因此无法通过此方式添加新键。

**记住：** 文件路径 `path` 需要在允许的路径中。在 [File cheatcodes](./fs.md)中阅读更多内容。

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

始终假定为根对象，因此我们可以通过以点(`.`)开头的路径引用其子对象。例如，`.boolean`，`.number` 和 `.obj1`。
我们可以深入到任意深度：`.obj1.aNumber` 或 `.obj1.obj2.aNumber`。
我们甚至可以在子树中搜索键：`.obj1..veryDeep`，或者只是 `..veryDeep`，因为没有歧义。

查看示例以了解其工作原理。

### 示例

#### 一个简单的示例

```solidity
string memory jsonObj = '{ "boolean": true, "number": 342, "myObject": { "title": "finally json serialization" } }';
vm.writeJson(jsonObj, "./output/example.json");

// replaces the value of `myObject` with a new object
string memory newJsonObj = '{ "aNumber": 123, "aString": "asd" }';
vm.writeJson(newJsonObj, "./output/example.json", ".myObject");

// replaces the value of `aString` in the new object
vm.writeJson("my new string", "./output/example.json", ".myObject.aString");

// Here's example.json:
// 
// {
//   "boolean": true,
//   "number": 342,
//   "myObject": {
//     "aNumber": 123,
//     "aString": "my new string"
//   }
// }
```

#### 一个更复杂的示例

```solidity
string memory jsonObj = '{ "boolean": true, "number": 342, "obj1": { "foo": "bar" } }';
vm.writeJson(jsonObj, "./output/example2.json");

string memory jsonObj2 = '{ "aNumber": 123, "obj2": {} }';
vm.writeJson(jsonObj2, "./output/example2.json", ".obj1");

string memory jsonObj3 = '{ "aNumber": 123, "obj3": { "veryDeep": 3 } }';
vm.writeJson(jsonObj3, "./output/example2.json", ".obj1.obj2");

// Here's example2.json so far:
//
// {
//   "boolean": true,
//   "number": 342,
//   "obj1": {
//     "aNumber": 123,
//     "obj2": {
//       "aNumber": 123,
//       "obj3": {
//         "veryDeep": 3
//       }
//     }
//   }
// }

// Note that the JSON object is just the value 13371337 in this case.
vm.writeJson("13371337", "./output/example2.json", "..veryDeep");

// Here's the final example2.json:
//
// {
//   "boolean": true,
//   "number": 342,
//   "obj1": {
//     "aNumber": 123,
//     "obj2": {
//       "aNumber": 123,
//       "obj3": {
//         "veryDeep": 13371337
//       }
//     }
//   }
// }
```

### 参见

- [serializeJson](./serialize-json.md)