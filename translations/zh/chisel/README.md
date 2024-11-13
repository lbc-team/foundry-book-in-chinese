## Chisel 概述

Chisel 是随 Foundry 提供的高级 Solidity REPL。它可用于在本地或分叉网络上快速测试 Solidity 片段。

Chisel 是 Foundry 套件的一部分，与 `forge`、`cast` 和 `anvil` 一起安装。如果你尚未安装 Foundry 请参阅 [Foundry 安装](../getting-started/installation.md)。

> 注意：如果已经安装了旧版 Foundry，则需要重新安装 `foundryup` 才能下载 Chisel。

### 如何使用 Chisel

要使用 Chisel，只需键入 `chisel`。然后开始编写 Solidity 代码！Chisel 会对每次输入提供详细反馈。

Chisel 可在 Foundry 项目内外使用。如果二进制文件在 Foundry 项目根目录下执行，Chisel 将继承项目的配置选项。

要查看可用命令，请在 REPL 中键入 `!help`。

> 📚 **参考资料**
>
> 有关 Chisel 及其功能的更多信息，请参阅 [`chisel` 参考资料](../reference/chisel/)。

