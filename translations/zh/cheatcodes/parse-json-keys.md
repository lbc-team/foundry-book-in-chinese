## `parseJsonKeys`

### 签名

```solidity
// 获取 JSON 字符串中存在的键列表
function parseJsonKeys(string calldata json, string calldata key) external pure returns (string[] memory keys);
```

### 描述

获取 JSON 字符串中存在的键列表

### 例子

```solidity
string memory json = '{"key": {"a": 1, "b": 2}}';
string[] memory keys = vm.parseJsonKeys(json, ".key"); // ["a", "b"]
```

```solidity
string memory json = '{"key": "something"}';
string[] memory keys = vm.parseJsonKeys(json, "$"); // ["key"]
```

```solidity
string memory json = '{"root_key": [{"a": 1, "b": 2}]}';
string[] memory keys = vm.parseJsonKeys(json, ".root_key[0]"); // ["a", "b"]
```