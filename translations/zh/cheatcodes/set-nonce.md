## `setNonce`

### 签名

```solidity
function setNonce(address account, uint64 nonce) external;
```

### 描述

设置给定账户的 nonce。

新的 nonce 必须高于账户当前的 nonce。

### 例子

```solidity
vm.setNonce(address(100), 1234);
```
