## `pauseGasMetering`

### 签名

```solidity
function pauseGasMetering() external;
```

### 描述

暂停 gas 计量（即 `gasleft()` 在执行操作时不会减少）。

这对于更好地了解 gas 成本很有用，可以通过关闭不必要的代码的 gas 计量，以及长时间运行的脚本，否则会耗尽 gas。

> ℹ️ **注意**
>
> `pauseGasMetering` *关闭了来自计量 gas 使用的 DoS 保护*。
>
> 暴露一个假设特定 EVM 实例将由于 gas 使用而完成的服务不再成立，这种情况下应该启用超时。 