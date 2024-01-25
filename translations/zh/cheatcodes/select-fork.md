## `selectFork`

### 签名

```solidity
function selectFork(uint256 forkId) external;
```

### 描述

使用 `createFork` 创建的分叉标识符，并将相应的分叉状态设置为活动状态。

### 例子

选择先前创建的分叉：

```solidity
uint256 forkId = vm.createFork(MAINNET_RPC_URL);

vm.selectFork(forkId);

assertEq(vm.activeFork(), forkId);
```

### 另请参阅

- [createFork](./create-fork.md)
- [activeFork](./active-fork.md)
