## `assertApproxEqAbs`

### 签名

```solidity
function assertApproxEqAbs(uint256 left, uint256 right, uint256 maxDelta) internal;
```

```solidity
function assertApproxEqAbs(uint256 left, uint256 right, uint256 maxDelta, string memory err) internal;
```

```solidity
function assertApproxEqAbs(int256 left, int256 right, uint256 maxDelta) internal;
```

```solidity
function assertApproxEqAbs(int256 left, int256 right, uint256 maxDelta, string memory err) internal;
```

### 描述

断言 `left` 与 `right` 在绝对值上近似相等，允许有一个 delta。

可选地在回退字符串中包含错误消息。

### 例子

```solidity
function testFail() external {
    uint256 a = 100;
    uint256 b = 200;

    assertApproxEqAbs(a, b, 90);
}
```

```ignore
[PASS] testFail() (gas: 23169)
Logs:
  Error: a ~= b not satisfied [uint]
    Expected: 200
      Actual: 100
   Max Delta: 90
       Delta: 100
```

### 参见

- [`assertApproxEqAbsDecimal`](./assertApproxEqAbsDecimal.md)
- [`assertApproxEqRel`](./assertApproxEqRel.md)