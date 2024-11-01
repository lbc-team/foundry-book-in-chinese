## `keyExists`

### Status

`keyExists` 正在被弃用，建议使用 `keyExistsJson`。它将在未来的版本中被移除。

### 签名

```solidity
// 检查 JSON 字符串中是否存在键。
vm.keyExists(string memory json, string memory key) returns (bool)
```

### 描述

检查 JSON 字符串中是否存在键。

### 例子

```solidity
string memory path = "./path/to/jsonfile.json";
string memory json = vm.readFile(path);
bool exists = vm.keyExists(json, ".key");
assertTrue(exists);
```
