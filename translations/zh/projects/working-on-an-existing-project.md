## 在现有项目工作

如果你下载一个使用 Foundry 的现有项目，那真的很容易上手。

对于这个例子，我们将使用[PaulRBerg][paul]的[`foundry-template`][template]。

首先，克隆该项目并在项目目录中运行 [`forge install`][install]。

```sh
$ git clone https://github.com/PaulRBerg/foundry-template
$ cd foundry-template
$ forge install
$ bun install # 安装 Solhint, Prettier, 以及其他 Node.js 依赖项。
```

我们运行 [`forge install`][install] 来安装项目中的子模块依赖项。

要构建，请使用 [`forge build`][build]:

```sh
{{#include ../output/foundry-template/forge-build:all}}
```

要进行测试，请使用 [`forge test`][test]:

```sh
{{#include ../output/foundry-template/forge-test:all}}
```

[paul]: https://github.com/PaulRBerg
[template]: https://github.com/PaulRBerg/foundry-template
[install]: ../reference/forge/forge-install.md
[build]: ../reference/forge/forge-build.md
[test]: ../reference/forge/forge-test.md