## `resetGasMetering`

### 签名

```solidity
function resetGasMetering() external;
```

### 描述

将 gas 计量重置为当前执行帧的 gas 限制（即 `gasleft()` 将恢复为初始值）。