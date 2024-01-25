## `rollFork`

### 签名

```solidity
// 将 _active_ 分叉滚动到给定的区块
function rollFork(uint256 blockNumber) external;
```

```solidity
// 将 _active_ 分叉滚动到事务被挖掘的区块，并重放所有先前执行的事务
function rollFork(bytes32 transaction) external;
```

```solidity
// 与 `rollFork(uint256 blockNumber)` 相同，但使用与 `forkId` 对应的分叉
function rollFork(uint256 forkId, uint256 blockNumber) external;
```

```solidity
// 与 `rollFork(bytes32 transaction)` 相同，但使用与 `forkId` 对应的分叉
function rollFork(uint256 forkId, bytes32 transaction) external;
```

### 描述

设置 `block.number`。如果作为参数传递了分叉标识符，则将更新该分叉，否则将更新当前活动的分叉。

如果提供了事务哈希，则将分叉滚动到挖掘该事务的区块，并重放所有先前执行的事务。

### 例子

为当前活动的分叉设置 `block.number`：

```solidity
uint256 forkId = vm.createFork(MAINNET_RPC_URL);
vm.selectFork(forkId);

assertEq(block.number, 15_171_037); // as of time of writing, 2022-07-19 04:55:27 UTC

vm.rollFork(15_171_057);

assertEq(block.number, 15_171_057);
```

为通过传递的 `forkId` 参数标识的分叉设置 `block.number`：

```solidity
uint256 optimismForkId = vm.createFork(OPTIMISM_RPC_URL);

vm.rollFork(optimismForkId, 1_337_000);

vm.selectFork(optimismForkId);

assertEq(block.number, 1_337_000);
```

### 另请参阅

- [roll](./roll.md)
- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)
- [activeFork](./active-fork.md)