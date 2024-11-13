## 创建一个新项目

要使用 Foundry 启动一个新项目，请使用 [`forge init`](../reference/forge/forge-init.md)：

```sh
{{#include ../output/hello_foundry/forge-init:command}}
```

这将从默认模板创建一个新目录 `hello_foundry` 。 这也会初始化一个新的 `git` 存储库。

如果你想使用不同的模板创建一个新项目，你可以传递 `--template` 指令，如下所示：

```sh
$ forge init --template https://github.com/foundry-rs/forge-template hello_template
```

现在，让我们检查一下默认模板的样子：

```sh
$ cd hello_foundry
{{#include ../output/hello_foundry/tree:all}}
```

默认模板安装了一个依赖项：Forge 标准库。 这是用于 Foundry 项目的首选测试库。 此外，该模板还附带一个空的入门合约和一个简单的测试。

让我们构建项目：

```sh
{{#include ../output/hello_foundry/forge-build:all}}
```

并运行测试：

```sh
{{#include ../output/hello_foundry/forge-test:all}}
```

你会注意到产生了两个新目录：`out` 和 `cache`。

`out` 目录包含你的合约工件(artifact，例如 ABI，而 `cache` 目录被 `forge` 使用来（记录），以便仅仅去重新编译那些必要编译的内容。