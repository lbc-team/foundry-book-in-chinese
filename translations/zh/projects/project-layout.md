Forge 在构建项目的方式上是灵活的。 默认情况下，Forge 项目结构为：

```ignore
{{#include ../output/hello_foundry/tree-with-files:output}}
```

- 你可以使用 `foundry.toml` 配置 Foundry 的行为。
- 重新映射在 `remappings.txt` 中指定。
- 合约的默认目录是 `src/`。
- 测试的默认目录是`test/`，其中任何具有以`test`开头的函数的合约都被视为测试。
- 依赖项作为 git 子模块存储在 `lib/` 中。

你可以分别使用 `--lib-paths` 和 `--contracts` 标志配置 Forge 在何处查找依赖项和合约。 或者，你可以在 foundry.toml 中配置它。

结合重新映射，这为你提供了支持其他工具链（例如 Hardhat 和 Truffle）的项目结构所需的灵活性。

对于获得自动 Hardhat 支持，你还可以传递 `--hh` 标志，它设置以下标志：`--lib-paths node_modules --contracts contracts`。