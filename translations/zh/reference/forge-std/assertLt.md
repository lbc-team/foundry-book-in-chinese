## `assertLt`

### 签名

```solidity
function assertLt(uint256 left, uint256 right) internal;
```

```solidity
function assertLt(uint256 left, uint256 right, string memory err) internal;
```

```solidity
function assertLt(int256 left, int256 right) internal;
```

```solidity
function assertLt(int256 left, int256 right, string memory err) internal;
```

### 描述

断言 `left` 严格小于 `right`。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertLtDecimal`](./assertLtDecimal.md)
- [`assertLe`](./assertLe.md)