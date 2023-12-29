## `getNonce`

### 签名

```solidity
function getNonce(address account) external returns (uint64);
```

```solidity
function getNonce(Wallet memory wallet) external returns (uint64);
```

### 描述

获取给定账户或 [钱包](./create-wallet.md)的 nonce。

### 示例

#### `address`
```solidity
uint256 nonce = vm.getNonce(address(100));
emit log_uint(nonce); // 0
```

#### `Wallet`
```solidity
Wallet memory alice = vm.createWallet("alice");
uint256 nonce = vm.getNonce(nonce);
emit log_uint(nonce); // 0
```