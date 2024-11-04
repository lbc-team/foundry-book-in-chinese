## `assertGt`

### 签名

```solidity
function assertGt(uint256 left, uint256 right) internal;
```

```solidity
function assertGt(uint256 left, uint256 right, string memory err) internal;
```

```solidity
function assertGt(int256 left, int256 right) internal;
```

```solidity
function assertGt(int256 left, int256 right, string memory err) internal;
```

### 描述

断言 `left` 严格大于 `right`。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertGtDecimal`](./assertGtDecimal.md)
- [`assertGe`](./assertGe.md)