## `difficulty`

### 签名

```solidity
function difficulty(uint256) external;
```

### 描述

设置 `block.difficulty`.

如果与后合并的 EVM 版本（从巴黎版本开始）一起使用，它将会回滚。在这种情况下，请改用[`vm.prevrandao`][prevrandao]。 

### 例子

```solidity
vm.difficulty(25);
emit log_uint(block.difficulty); // 25
```

[prevrandao]: ./prevrandao.md