## `assertApproxEqRel`

### 签名

```solidity
function assertApproxEqRel(uint256 left, uint256 right, uint256 maxPercentDelta) internal;
```

```solidity
function assertApproxEqRel(uint256 left, uint256 right, uint256 maxPercentDelta, string memory err) internal;
```

```solidity
function assertApproxEqRel(int256 left, int256 right, uint256 maxPercentDelta) internal;
```

```solidity
function assertApproxEqRel(int256 left, int256 right, uint256 maxPercentDelta, string memory err) internal;
```

### 描述

断言 `left` 与 `right` 在百分比上大致相等，`1e18` 表示 100%。

可选地在回退字符串中包含错误消息。

### 例子

```solidity
function testFail () external {
    uint256 a = 100;
    uint256 b = 200;
    assertApproxEqRel(a, b, 0.4e18);
}
```

```ignore
[PASS] testFail() (gas: 23884)
Logs:
  Error: a ~= b not satisfied [uint]
      Expected: 200
        Actual: 100
   Max % Delta: 0.400000000000000000
       % Delta: 0.500000000000000000
```

### 参见

- [`assertApproxEqRelDecimal`](./assertApproxEqRelDecimal.md)
- [`assertApproxEqAbs`](./assertApproxEqAbs.md)