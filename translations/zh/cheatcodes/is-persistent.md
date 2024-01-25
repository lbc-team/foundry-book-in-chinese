## `isPersistent`

### 签名

```solidity
 function isPersistent(address) external returns (bool);
```

### 描述

返回一个账户是否被标记为持久化（[`makePersistent`](./make-persistent.md)）。

### 例子

检查 `msg.sender` 的默认状态和当前测试账户

```solidity
// By default the `sender` and the test contract itself are persistent
assert(cheats.isPersistent(msg.sender));
assert(cheats.isPersistent(address(this)));
```

### 另请参阅

- [makePersistent](./make-persistent.md)
- [revokePersistent](./revoke-persistent.md)  
