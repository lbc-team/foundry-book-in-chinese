## `prevrandao`

### 签名

```solidity
function prevrandao(bytes32) external;
```

### 描述

设置 `block.prevrandao`。

如果与 Paris 硬分叉之前的 EVM 版本一起使用，它将 revert。在这种情况下，改用 [`vm.difficulty`][prevrandao]。

### 例子

```solidity
vm.prevrandao(bytes32(uint256(42)));
emit log_uint(block.prevrandao); // 42
```

[prevrandao]: ./difficulty.md {/try-react/}