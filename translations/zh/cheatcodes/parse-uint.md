## `parseUint`

### 签名

```solidity
function parseUint(string calldata stringifiedValue) external pure returns (uint256 parsedValue);
```

### 描述

将 `string` 的值解析为 `uint256`

### 例子

```solidity
string memory uintAsString = "12345";
uint256 stringToUint = vm.parseUint(uintAsString); // 12345
```
