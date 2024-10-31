## `keyExistsToml`

### 签名

```solidity
// 检查 TOML 表格中是否存在某个键。
vm.keyExistsToml(string memory toml, string memory key) returns (bool)
```

### 描述

检查一个键是否存在于 TOML 表中。

### 示例

```solidity
string memory path = "./path/to/tomlfile.toml";
string memory toml = vm.readFile(path);
bool exists = vm.keyExistsToml(toml, ".key");
assertTrue(exists);
```