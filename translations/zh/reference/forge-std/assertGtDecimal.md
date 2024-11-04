## `assertGtDecimal`

### 签名

```solidity
function assertGtDecimal(uint256 left, uint256 right, uint256 decimals) internal
```

```solidity
function assertGtDecimal(uint256 left, uint256 right, uint256 decimals, string memory err) internal;
```

```solidity
function assertGtDecimal(int256 left, int256 right, uint256 decimals) internal;
```

```solidity
function assertGtDecimal(int256 left, int256 right, uint256 decimals, string memory err) internal;
```

### 描述

断言 `left` 严格大于 `right`。

`left` 和 `right` 在回退字符串中以小数格式显示。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertGt`](./assertGt.md)