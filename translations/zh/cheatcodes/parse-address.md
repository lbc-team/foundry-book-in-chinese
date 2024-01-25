## `parseAddress`

### 签名

```solidity
function parseAddress(string calldata stringifiedValue) external pure returns (address parsedValue);
```

### 描述

将`string`的值解析为`address`

### 例子

```solidity
string memory addressAsString = "0x0000000000000000000000000000000000000000";
address stringToAddress = vm.parseAddress(addressAsString); // 0x0000000000000000000000000000000000000000
```