## `parseBool`

### 签名

```solidity
function parseBool(string calldata stringifiedValue) external pure returns (bool parsedValue);
```

### 描述

将 `string` 的值解析为 `bool`

### 例子

```solidity
string memory boolAsString = "false";
bool stringToBool = vm.parseBool(boolAsString); // false
```