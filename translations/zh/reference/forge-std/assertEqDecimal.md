## `assertEqDecimal`

### 签名

```solidity
function assertEqDecimal(uint256 left, uint256 right, uint256 decimals) internal
```

```solidity
function assertEqDecimal(uint256 left, uint256 right, uint256 decimals, string memory err) internal;
```

```solidity
function assertEqDecimal(int256 left, int256 right, uint256 decimals) internal;
```

```solidity
function assertEqDecimal(int256 left, int256 right, uint256 decimals, string memory err) internal;
```

### 描述

断言 `left` 等于 `right`。

`left` 和 `right` 在回退字符串中以小数格式显示。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertEq`](./assertEq.md)