## `prank`

### 签名

```solidity 
function prank(address) external;
```

```solidity
function prank(address sender, address origin) external;
```

### 描述

将`msg.sender`设置为指定地址 **用于下一次调用**。"下一次调用" 包括静态调用，但不包括对作弊码地址的调用。

如果使用 `prank` 的另一种签名，则还会为下一次调用设置 `tx.origin`。

### 例子

```solidity
/// function withdraw() public {
///     require(msg.sender == owner);

vm.prank(owner);
myContract.withdraw(); // [PASS]
```

### 另请参阅

Forge 标准库

[`hoax`](../reference/forge-std/hoax.md) 