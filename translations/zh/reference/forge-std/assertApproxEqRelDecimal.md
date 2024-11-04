## `assertApproxEqRelDecimal`

### 签名

```solidity
function assertApproxEqRelDecimal(uint256 left, uint256 right, uint256 maxPercentDelta, uint256 decimals) internal;
```

```solidity
function assertApproxEqRelDecimal(uint256 left, uint256 right, uint256 maxPercentDelta, uint256 decimals, string memory err) internal;
```

```solidity
function assertApproxEqRelDecimal(int256 left, int256 right, uint256 maxPercentDelta, uint256 decimals) internal;
```

```solidity
function assertApproxEqRelDecimal(int256 left, int256 right, uint256 maxPercentDelta, uint256 decimals, string memory err) internal;
```

### 描述

断言 `left` 大约等于 `right`，以百分比的增量为准，其中 `1e18` 代表 100%。

`left` 和 `right` 在回退字符串中以小数格式显示。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertApproxEqRel`](./assertApproxEqRel.md)