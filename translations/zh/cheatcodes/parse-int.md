## `parseInt`

### 签名

```solidity
function parseInt(string calldata stringifiedValue) external pure returns (int256 parsedValue);
```

### 描述

将 `string` 的值解析为 `int256`

### 例子

```solidity
string memory intAsString = "-12345";
int256 stringToInt = vm.parseInt(intAsString); // -12345
```