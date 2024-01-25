## `createFork`

### 签名

```solidity
// 使用给定的端点创建一个新的分叉，并返回该分叉的标识符
function createFork(string calldata urlOrAlias) external returns (uint256)
```

```solidity
// 创建一个新的分叉，并选择使用给定的端点和块，并返回该分叉的标识符
function createFork(string calldata urlOrAlias, uint256 block) external returns (uint256);
```

```solidity
// 使用给定的端点创建一个新的分叉，并在给定交易所在的块处开始，并重放该交易之前的所有交易
function createFork(string calldata urlOrAlias, bytes32 transaction) external returns (uint256);
```

### 描述

从给定的端点创建一个新的分叉，并返回该分叉的标识符。如果作为参数传递了块号，则该分叉将从该块开始，否则将从最新的块开始。

如果提供了交易哈希，则将该分叉滚动到包含该交易的块，并重放所有先前执行的交易。

### 例子

使用最新的块号创建一个新的主网分叉：

```solidity
uint256 forkId = vm.createFork(MAINNET_RPC_URL);
vm.selectFork(forkId);

assertEq(block.number, 15_171_037); // as of time of writing, 2022-07-19 04:55:27 UTC
```

使用给定的块号创建一个新的主网分叉：

```solidity
uint256 forkId = vm.createFork(MAINNET_RPC_URL, 1_337_000);
vm.selectFork(forkId);

assertEq(block.number, 1_337_000);
```

### 另请参阅

- [activeFork](./active-fork.md)
- [selectFork](./select-fork.md)
- [createSelectFork](./create-select-fork.md)
