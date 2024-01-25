## `resumeGasMetering`

### 签名

```solidity
function resumeGasMetering() external;
```

### 描述

恢复 Gas 计量（即 `gasleft()` 会随着操作的执行而减少）。Gas 使用量将在暂停时的相同数量上恢复。