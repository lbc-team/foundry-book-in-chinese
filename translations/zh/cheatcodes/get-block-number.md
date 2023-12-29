## `getBlockNumber`

### 签名

```solidity
function getBlockNumber() external view returns (uint256 timestamp);
```

### 描述

获取当前的 `block.number`。在使用 `vm.roll` 和 `--via-ir` 编译的情况下，这是很有用的，因为在交易过程中，`block.number` 被假定为一个常数。这意味着在每次 Forge 测试中，对 `block.number` 的多次调用会被优化为只返回一个常数值，而不是实际访问当前的 `block.number`。`vm.getBlockNumber()` 避免了这种优化，并返回当前的 `block.number`。

### 例子

```solidity
uint256 height = vm.getBlockNumber();
assertEq(height, uint256(block.number));
vm.roll(10);
assertEq(vm.getBlockNumber(), 10);
```