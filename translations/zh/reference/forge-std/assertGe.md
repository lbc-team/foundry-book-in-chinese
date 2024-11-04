## `assertGe`

### 签名

```solidity
function assertGe(uint256 left, uint256 right) internal;
```

```solidity
function assertGe(uint256 left, uint256 right, string memory err) internal;
```

```solidity
function assertGe(int256 left, int256 right) internal;
```

```solidity
function assertGe(int256 left, int256 right, string memory err) internal;
```

### 描述

断言 `left` 大于或等于 `right`。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertGeDecimal`](./assertGeDecimal.md)
- [`assertGt`](./assertGt.md)