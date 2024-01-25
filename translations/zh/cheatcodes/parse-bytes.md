## `parseBytes`

### 签名

```solidity
function parseBytes(string calldata stringifiedValue) external pure returns (bytes memory parsedValue);
```

### 描述

将 `string` 的值解析为 `bytes`

### 例子

```solidity
string memory bytesAsString = "0x00000000000000000000000000000000";
bytes memory stringToBytes = vm.parseBytes(bytesAsString); // 0x00000000000000000000000000000000

```
