## `activeFork`

### 签名

```solidity
function activeFork() external returns (uint256);
```

### 描述

返回当前活动(active)分叉的标识符。如果当前没有活动分叉，则回滚(revert)。

### 示例

获取当前活动(active)分叉的 id：

```solidity
uint256 mainnetForkId = vm.createFork(MAINNET_RPC_URL);
uint256 optimismForkId = vm.createFork(OPTIMISM_RPC_URL);

assert(mainnetForkId != optimismForkId);

vm.selectFork(mainnetForkId);
assertEq(vm.activeFork(), mainnetForkId);

vm.selectFork(optimismForkId);
assertEq(vm.activeFork(), optimismForkId);
```

### 另请参阅

- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)
