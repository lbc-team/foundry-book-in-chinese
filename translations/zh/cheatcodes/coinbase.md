## `coinbase`

### 签名

```solidity
function coinbase(address) external;
```

### 描述

设置 `block.coinbase`.

### 例子

```solidity
emit log_address(block.coinbase); // 0x0000000000000000000000000000000000000000
vm.coinbase(0xEA674fdDe714fd979de3EdF0F56AA9716B898ec8);
emit log_address(block.coinbase); // 0xea674fdde714fd979de3edf0f56aa9716b898ec8
```