## `startPrank`

### 签名

```solidity
function startPrank(address) external;
```

```solidity
function startPrank(address sender, address origin) external;
```

### 描述

设置 `msg.sender` **用于所有后续调用**，直到调用 [`stopPrank`](./stop-prank.md) 为止。

如果使用了 `startPrank` 的另一种签名，则还会为所有后续调用设置 `tx.origin`。

### 另请参阅

Forge 标准库

[`startHoax`](../reference/forge-std/startHoax.md), [`changePrank`](../reference/forge-std/change-prank.md)
