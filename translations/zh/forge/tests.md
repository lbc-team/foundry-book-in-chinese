## 测试

Forge 可以使用 [`forge test`](../reference/forge/forge-test.md) 命令运行测试（用例）。 所有测试都是用 Solidity 编写的。

Forge 将在你的源目录中的任何位置查找测试。 任何具有以`test`开头的函数的合约都被认为是一个测试。 通常，测试将按照约定放在 `test` 中，并以 `.t.sol` 结尾。

下面是在新创建的项目中运行 `forge test` 的示例，该项目只有默认测试：

```sh
{{#include ../output/hello_foundry/forge-test:all}}
```

你还可以通过传递过滤器来运行特定测试：

```sh
{{#include ../output/test_filters/forge-test-match-contract-and-test:all}}
```

这将在名称中带有 `testDeposit` 的 `ComplicatedContractTest` 测试合约中运行测试。
这些标志的反向版本也存在（`--no-match-contract` 和 `--no-match-test`）。

你可以使用 `--match-path` 与 glob 模式匹配的文件名中运行测试。

```sh
{{#include ../output/test_filters/forge-test-match-path:all}}
```

`--match-path` 标志的反面是 `--no-match-path`。

### 日志和跟踪

`forge test` 的默认行为是只显示通过和失败测试的摘要。 你可以通过增加详细程度（使用`-v`标志）来控制此行为。 每个详细级别都会添加更多信息：

- **级别 2  (`-vv`)**：还会显示测试期间发出的日志。 这包括来自测试的断言错误，显示诸如预期与实际结果等之类的信息。
- **级别 3 (`-vvv`)**：还显示失败测试的堆栈跟踪。
- **级别 4 (`-vvvv`)**：显示所有测试的堆栈跟踪，并显示失败测试的设置（setup）跟踪。
- **级别 5 (`-vvvvv`)**：始终显示堆栈跟踪和设置（setup）跟踪。

### Watch模式

当你使用`forge test --watch`对文件进行更改时，Forge 可以重新运行你的测试。

默认情况下，仅重新运行更改的测试文件。 如果你想重新运行更改的所有测试，你可以使用 `forge test --watch --run-all`。