## `fee`

### 签名

```solidity
function fee(uint256) external;
```

### 描述

设置 `block.basefee`.

### 例子

```solidity
vm.fee(25 gwei);
emit log_uint(block.basefee); // 25000000000
```
