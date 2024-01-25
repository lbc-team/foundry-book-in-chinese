## `assumePayable`

### 签名

```solidity
function assumePayable(address addr) public;
```

### 描述

使用 [`assume`](../../cheatcodes/assume.md) 来过滤拒绝以太币转账的地址。

这将对指定的 `addr` 进行外部调用，不带调用数据，并检查 `assume` 与调用成功的结果。