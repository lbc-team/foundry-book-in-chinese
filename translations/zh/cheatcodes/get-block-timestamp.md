## `getBlockTimestamp`

### 签名

```solidity
function getBlockTimestamp() external view returns (uint256 timestamp);
```

### 描述

获取当前的 `block.timestamp`。在使用 `vm.warp` 和 `--via-ir` 编译的情况下，这是很有用的，因为在交易过程中 `block.timestamp` 被假定为一个常数。这意味着在每次 Forge 测试中，对 `block.timestamp` 的多次调用会被优化为只返回一个常数值，而不是实际访问当前的 `block.timestamp`。`vm.getBlockTimestamp()` 避免了这种优化，并返回当前的 `block.timestamp`。

### 示例

```solidity
assertEq(vm.getBlockTimestamp(), 1, "timestamp should be 1");
vm.warp(10);
assertEq(vm.getBlockTimestamp(), 10, "warp failed");
```