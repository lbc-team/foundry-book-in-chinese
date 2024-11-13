## Anvil 概述

Anvil 是 Foundry 附带的本地测试网节点。 你可以使用它从前端测试你的合约或通过 RPC 进行交互。

Anvil 是 Foundry 套件的一部分，与 `forge` 、 `cast` 和 `chisel` 一起安装。 如果你还没有安装 Foundry，请参阅 [Foundry installation](../getting-started/installation.md)。

> 注意：如果你安装了旧版本的 Foundry，则需要重新安装 `foundryup` 才能下载 Anvil。

### 如何使用 Anvil

要使用 Anvil，只需输入 `anvil`。 你应该会看到可用的帐户和私钥列表，以及节点正在侦听的地址和端口。

Anvil 是高度可配置的。 你可以运行 `anvil -h` 查看所有配置选项。

一些基本选项是：

```bash
#  Number of dev accounts to generate and configure. [default: 10]
anvil -a, --accounts <ACCOUNTS>

# The EVM hardfork to use. [default: latest]
anvil --hardfork <HARDFORK>

# Port number to listen on. [default: 8545]
anvil  -p, --port <PORT>
```

> 📚 **参考**
>
> 有关 Anvil 及其功能的深入信息，请参阅 [`anvil` 参考](../reference/anvil/)。

