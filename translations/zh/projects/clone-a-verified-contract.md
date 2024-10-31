## 克隆链上已验证的合约

要将链上已验证的合约克隆为 Forge 项目，请使用 [`forge clone`](../reference/forge/forge-clone.md)，例如以以太坊主网的 [WETH9](https://etherscan.io/address/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2) 为例：

```sh
{{#include ../output/clone_contract/forge-clone:command}}
```

这将创建一个新的目录 `WETH9`，将其配置为一个 Foundry 项目，并将合约的所有源代码克隆到其中。这还会初始化一个新的 `git` 仓库。

```sh
{{#include ../output/clone_contract/forge-clone:output}}
```

克隆的 Forge 项目除了普通 Forge 项目所需的文件外，还带有一个额外的 `.clone.meta` 元数据文件。

让我们看看 `.clone.meta` 文件的样子：

```sh
{{#include ../output/clone_contract/clone-meta:output}}
```

`clone.meta` 是一个紧凑的 JSON 数据文件，包含链上合约实例的信息，例如合约地址、构造函数参数等。有关元数据的更多详细信息，请参见 [reference](../reference/forge/forge-clone.md#metadata)。