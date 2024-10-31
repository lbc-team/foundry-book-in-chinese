# Gas 部分快照

Forge 可以在测试函数内部的任意部分捕获 gas 快照。这对于精确测量您的逻辑消耗了多少 gas 非常有用，因为外部调用和内部 gas 使用都会被测量。

您可以在测试中使用 `snapshotGas` [作弊代码](./cheatcodes.md) 来捕获 gas 使用，而不是运行像 `forge snapshot` 或 `forge test --gas-report` 这样的命令，如下所示：

{{#include ../cheatcodes/gas-snapshots.md}}