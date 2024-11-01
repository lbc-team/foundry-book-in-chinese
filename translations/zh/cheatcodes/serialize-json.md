## `serializeJson`

### 签名

```solidity
function serializeJson(string calldata objectKey, string calldata value)
    external
    returns (string memory json);

function serializeBool(string calldata objectKey, string calldata valueKey, bool value)
    external
    returns (string memory json);

function serializeUint(string calldata objectKey, string calldata valueKey, uint256 value)
    external
    returns (string memory json);

function serializeInt(string calldata objectKey, string calldata valueKey, int256 value)
    external
    returns (string memory json);

function serializeAddress(string calldata objectKey, string calldata valueKey, address value)
    external
    returns (string memory json);

function serializeBytes32(string calldata objectKey, string calldata valueKey, bytes32 value)
    external
    returns (string memory json);

function serializeString(string calldata objectKey, string calldata valueKey, string calldata value)
    external
    returns (string memory json);

function serializeBytes(string calldata objectKey, string calldata valueKey, bytes calldata value)
    external
    returns (string memory json);

function serializeBool(string calldata objectKey, string calldata valueKey, bool[] calldata values)
    external
    returns (string memory json);

function serializeUint(string calldata objectKey, string calldata valueKey, uint256[] calldata values)
    external
    returns (string memory json);

function serializeInt(string calldata objectKey, string calldata valueKey, int256[] calldata values)
    external
    returns (string memory json);

function serializeAddress(string calldata objectKey, string calldata valueKey, address[] calldata values)
    external
    returns (string memory json);

function serializeBytes32(string calldata objectKey, string calldata valueKey, bytes32[] calldata values)
    external
    returns (string memory json);

function serializeString(string calldata objectKey, string calldata valueKey, string[] calldata values)
    external
    returns (string memory json);

function serializeBytes(string calldata objectKey, string calldata valueKey, bytes[] calldata values)
    external
    returns (string memory json);
```

### 描述

将值序列化为字符串化的 JSON 对象。

### 工作原理

其思想是用户将 JSON 文件的值序列化，最终将该对象写入文件。用户需要传递以下内容：

- 一个用于将值序列化的 _object_ 的键。这使用户能够并行序列化多个对象
- 一个用于 _value_ 的键，它将成为 JSON 文件中的键
- 要序列化的值

`serializeJson` 函数的一个例外是，它只接收 `objectKey` 和 json 字符串 `value`。这使用户能够序列化现有的 JSON 对象，并直接将其分配给提供的 `objectKey`。如果 `objectKey` 已在使用中，则整个序列化的 json 将被覆盖。

这些键不需要具有特定形式。它们是 `string` 类型，以便直观地进行人类解释。从语义上讲，除了用作键之外，它们并不重要。

作弊码返回**到目前为止**正在被序列化的 JSON 对象。这样用户可以序列化内部 JSON 对象，然后将它们序列化为更大的 JSON 对象，从而使用户能够创建任意的 JSON 对象。

最后，用户可以使用 [writeJson](./write-json.md) 将 JSON 对象写入 JSON 文件。
或者，用户可以使用 [writeToml](./write-toml.md) 将 JSON 对象写入 TOML 文件。

**记住：** 文件路径需要在允许的路径中。在 [File cheatcodes](./fs.md)中阅读更多信息。

### 示例

假设我们想要将以下 JSON 写入文件：

{ "boolean": true, "number": 342, "object": { "title": "finally json serialization" } }

```solidity
string memory obj1 = "some key";
vm.serializeBool(obj1, "boolean", true);
vm.serializeUint(obj1, "number", uint256(342));

string memory obj2 = "some other key";
string memory output = vm.serializeString(obj2, "title", "finally json serialization");

// IMPORTANT: This works because `serializeString` first tries to interpret `output` as
//   a stringified JSON object. If the parsing fails, then it treats it as a normal
//   string instead.
//   For instance, an `output` equal to '{ "ok": "asd" }' will produce an object, but
//   an output equal to '"ok": "asd" }' will just produce a normal string.
string memory finalJson = vm.serializeString(obj1, "object", output);

vm.writeJson(finalJson, "./output/example.json");
```

### 另请参阅

- [writeJson](./write-json.md)