## `assumeNotPrecompile`

### 签名

```solidity
function assumeNotPrecompile(address addr) public;
```

```solidity
function assumeNotPrecompile(address addr, uint256 chainid) public;
```

### 描述

使用 [`assume`](../../cheatcodes/assume.md) 来从模糊测试中过滤预编译地址。

可选地，可以指定 `chainid` 来过滤在相应链上已知的预编译。

### 另请参阅

[Precompile Registry](../../misc/precompile-registry.md)