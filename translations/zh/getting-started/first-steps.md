## Foundry 的第一步

本节介绍了 `forge` 命令行工具。我们将逐步创建一个新项目，编译它，并运行测试。

要使用 Foundry 启动一个新项目，请使用 [`forge init`](../reference/forge/forge-init.md) 命令：

```sh
{{#include ../output/hello_foundry/forge-init:command}}
```

现在，让我们探索一下 `forge` 为我们生成的结构：

```sh
$ cd hello_foundry
{{#include ../output/hello_foundry/tree:all}}
```

你可以使用 [`forge build`](../reference/forge/forge-build.md) 编译项目：

```sh
{{#include ../output/hello_foundry/forge-build:all}}
```

要运行测试，请使用 [`forge test`](../reference/forge/forge-test.md) 命令：

```sh
{{#include ../output/hello_foundry/forge-test:all}}
```

<br>

> 💡 **提示**
>
> 你可以随时通过在任何命令或子命令后附加 `--help` 来查看详细帮助。

对于视觉学习者，请务必查看这些[初学者教程](../tutorials/learn-foundry.md) 。