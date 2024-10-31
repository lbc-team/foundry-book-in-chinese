## `keyExistsJson`

### 签名

```solidity
// Check if a key exists in a JSON string.
vm.keyExistsJson(string memory json, string memory key) returns (bool)
```

### 描述

检查一个键是否存在于 JSON 字符串中。

### 示例

```solidity
string memory path = "./path/to/jsonfile.json";
string memory json = vm.readFile(path);
bool exists = vm.keyExistsJson(json, ".key");
assertTrue(exists);
```