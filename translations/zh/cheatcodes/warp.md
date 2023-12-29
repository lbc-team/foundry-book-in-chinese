## `warp`

### 签名

```solidity
function warp(uint256) external;
```

### 描述

设置 `block.timestamp`.

### 例子

```solidity
vm.warp(1641070800);
emit log_uint(block.timestamp); // 1641070800
```

### 另请参阅

Forge 标准库

[`skip`](../reference/forge-std/skip.md), [`rewind`](../reference/forge-std/rewind.md)