## `parseTomlKeys`

### 签名

```solidity
// Get list of keys present in a TOML string
function parseTomlKeys(string calldata toml, string calldata key) external pure returns (string[] memory keys);
```

### 描述

获取存在于 TOML 字符串中的键的列表

### 示例

```solidity
// [key]
// a = 1
// b = 2

string memory toml = '[key]\n a = 1\n b = 2';
string[] memory keys = vm.parseTomlKeys(toml, ".key"); // ["a", "b"]
```

```solidity
// key = "something"

string memory toml = 'key = \"something\"';
string[] memory keys = vm.parseTomlKeys(toml, "$"); // ["key"]
```

```solidity
// [[root_key]]
// a = 1
// b = 2

string memory toml = '[[root_key]]\n a = 1\n b = 2';
string[] memory keys = vm.parseTomlKeys(toml, ".root_key.0"); // ["a", "b"]
```