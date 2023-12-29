## `chainId`

### 签名

```solidity
function chainId(uint256) external;
```

### 描述

设置 `block.chainid`.

### 例子

```solidity
vm.chainId(31337);
emit log_uint(block.chainid); // 31337
```
