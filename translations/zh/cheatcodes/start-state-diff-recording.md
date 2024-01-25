## `startStateDiffRecording`

### 签名

```solidity
function startStateDiffRecording()
```

### 描述

记录所有状态更改作为 CREATE、CALL 或 SELFDESTRUCT 操作码的一部分，以及调用的上下文。
有关如何访问和解释记录的状态更改的更多详细信息，请参阅 [`stopAndReturnStateDiff`](./stop-and-return-state-diff.md)。 