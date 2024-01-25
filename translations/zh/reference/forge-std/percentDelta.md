## `percentDelta`

### 签名

```solidity
function percentDelta(uint256 a, uint256 b) internal pure returns (uint256)
```

```solidity
function percentDelta(int256 a, int256 b) internal pure returns (uint256)
```

### 描述

返回两个数字之间的差值，以百分比表示，其中 `1e18` 是 100%。
更准确地说，`percentDelta(a, b)` 计算 `abs((a-b) / b ) * 1e18`。

### 例子

```solidity
uint256 percent150 = stdMath.percentDelta(uint256(125), 50);
uint256 percent60 = stdMath.percentDelta(uint256(50), 125);
```
