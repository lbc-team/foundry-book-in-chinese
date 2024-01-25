## `createSelectFork`

### 签名

```solidity
function createSelectFork(string calldata urlOrAlias) external returns (uint256);
```

```solidity
function createSelectFork(string calldata urlOrAlias, uint256 block) external returns (uint256);
```

```solidity
function createSelectFork(string calldata urlOrAlias, bytes32 transaction) external returns (uint256);
```

### 描述

从给定的端点创建并选择一个新的 fork，并返回该 fork 的标识符。如果将块号作为参数传递，则该 fork 将从该块开始，否则将从最新的块开始。

如果提供了交易哈希，它将将分叉滚动到交易所在的区块，并重放所有先前执行的交易。

### 例子

使用最新的块号创建并选择一个新的主网 fork：

```solidity
uint256 forkId = vm.createSelectFork(MAINNET_RPC_URL);

assertEq(block.number, 15_171_037); // as of time of writing, 2022-07-19 04:55:27 UTC
```

使用给定的块号创建并选择一个新的主网 fork：

```solidity
uint256 forkId = vm.createSelectFork(MAINNET_RPC_URL, 1_337_000);

assertEq(block.number, 1_337_000);
```

### 另请参阅

- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)