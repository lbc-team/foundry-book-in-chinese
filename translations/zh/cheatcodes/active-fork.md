## `activeFork`

### 签名

```solidity
function activeFork() external returns (uint256);
```

### 描述

返回当前 active 的分叉，如果不存在则 revert 。

### 示例

获取当前 active 的分叉 id:

```solidity
uint256 mainnetForkId = vm.createFork(MAINNET_RPC_URL);
uint256 optimismForkId = vm.createFork(OPTIMISM_RPC_URL);

assert(mainnetForkId != optimismForkId);

vm.selectFork(mainnetForkId);
assertEq(vm.activeFork(), mainnetForkId);

vm.selectFork(optimismForkId);
assertEq(vm.activeFork(), optimismForkId);
```

### 参见

- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)
