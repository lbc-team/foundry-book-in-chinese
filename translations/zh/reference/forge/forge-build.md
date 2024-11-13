## forge build

### 名称

forge-build - 构建项目的智能合约。

### 简介

``forge build`` 或 ``forge b`` [*options*]

### 描述

构建项目的智能合约。

该命令将通过查看所有合约和依赖的版本要求，尝试检测能够编译你的项目的最新版本。

你可以通过传递 `--no-auto-detect` 来覆盖这一行为。或者，你也可以通过 `--use <SOLC_VERSION>`。

如果该命令检测到它用来构建的 Solidity 编译器版本没有安装，它将下载它并安装在 `~/.svm` 中。你可以通过传递 `--offline` 来禁用这种行为。

构建是增量的，构建缓存默认保存在项目根目录下的 `cache/`。如果你想清除缓存，请传递 `--force`，如果你想改变缓存目录，请传递 `--cache-path <PATH>`。

可以通过指定多个路径选项（可以是源目录或文件的路径）来选择构建源。

#### Build 模式

有三种 build 模式:

- 只是编译（默认）：构建项目并将合约 artifacts 保存在 `out/`（或由 `--out <PATH>` 指定的路径）。
- 大小模式（`--sizes`）：构建项目，显示非测试合约的大小，如果任何合约超过大小限制，则以代码 1 退出。
- 命名模式（`--names`）：构建项目，显示合约的名称并退出。

#### 优化器

你可以通过 `--optimize` 来启用优化器，你可以通过 `--optimizer-runs <RUNS>` 来调整优化器的 runs 数量。

你也可以通过传递 `--via-ir` 来选择加入 Solidity IR 编译管道。在 [Solidity 文档][ir-pipeline] 中阅读更多关于 IR 管道的信息。

默认情况下，优化器被启用并设置 runs 为 200 个周期。

##### 有条件使用优化器

许多项目使用 solc 优化器，无论是通过标准编译管道还是 IR 管道。但在某些情况下，优化器会大大降低编译速度。

对于常规编译，使用优化器的项目配置文件可能如下所示：

```toml
[profile.default]
solc-version = "0.8.17"
optimizer = true
optimizer-runs = 10_000_000
```

或者像这样的 `via-ir`：

```toml
[profile.default]
solc-version = "0.8.17"
via_ir = true
```

为了减少开发和测试过程中的编译时间，一种方法是有一个关闭优化器的 `lite` 配置文件，并在开发/测试周期中使用它。用于常规编译的的配置文件可能看起来像这样：

```toml
[profile.default]
solc-version = "0.8.17"
optimizer = true
optimizer-runs = 10_000_000

[profile.lite]
optimizer = false
```

或者像这样的 `via-ir`：

```toml
[profile.default]
solc-version = "0.8.17"
via_ir = true

[profile.lite.optimizer_details.yulDetails]
optimizerSteps = ''
```

当这样设置时，`forge build`（或 `forge test`/`forge script`）仍然使用标准配置文件，所以默认情况下，`forge script` 调用将以生产环境设置部署你的合约。运行 `FOUNDRY_PROFILE=lite forge build`（同样，test 和 script 命令也是如此）将使用 lite 配置文件来减少编译时间。

> 还有一些额外的优化器细节可以配置，更多信息见下面的 [额外优化器设置](#additional-optimizer-settings) 部分。

#### Artifacts

你可以通过传递 `--extra-output <SELECTOR>` 将 Solidity 编译器的额外输出添加到你的 artifacts 中。

选择器是 Solidity 编译器输出中的一个路径，你可以在 [Solidity 文档][output-desc] 中阅读更多关于它的信息。

你也可以通过传递 `--extra-output-files <SELECTOR>` 将一些编译器的输出写入单独的文件中。

`--extra-output-files` 有效选择器是：

- `metadata`: 在 artifacts 目录中写入 `metadata.json` 文件
- `ir`: 在 artifacts 目录中写入 `.ir` 文件
- `irOptimized`: 在 artifacts 目录中写入 `.iropt` 文件
- `ewasm`: 在 artifacts 目录中写入 `.ewasm` 文件
- `evm.assembly`: 在 artifacts 目录中写入 `.asm` 文件

#### 监视模式

该命令可以通过传递 `--watch [PATH...]` 在监视模式下运行，每当被监视的文件或目录发生变化时就会重建。默认情况下，源目录是被监视的。

#### 稀疏模式（实验性）

稀疏模式只编译符合某些标准的文件。

默认情况下，这个过滤器适用于自上次构建以来没有被改变的文件，但对于接受文件过滤器的命令（例如[forge test](./forge-test.md)），稀疏模式将只重新编译符合过滤器的文件。

稀疏模式是选择加入的，直到该功能稳定下来。要选择加入稀疏模式并试用它，请在你的配置文件中设置[`sparse_mode`][sparse-mode]。

#### 优化器额外设置

优化器可以通过额外的设置进行微调。只需在你的配置文件中设置 `optimizer_details` 数据。比如说。

```toml
[profile.default.optimizer_details]
constantOptimizer = true
yul = true

[profile.default.optimizer_details.yulDetails]
stackAllocation = true
optimizerSteps = 'dhfoDgvulfnTUtnIf'
```

参见 [编译器输入描述文档](https://docs.soliditylang.org/en/latest/using-the-compiler.html#compiler-input-and-output-json-description) 获取更多关于可用设置的信息（特别是 `settings.optimizer.details`）。

#### Revert 设置

你可以控制编译器如何生成 revert 字符串。默认情况下，只有用户提供的 revert 字符串被包含在字节码中，但也有其他选项。

- `strip` 删除所有的 revert 字符串（如果可能的话，即如果使用了字面量），以保持副作用。
- `debug` 为编译器生成的内部 revert 注入字符串，目前为ABI编码器 V1 和 V2 实现。
- `verboseDebug` 甚至为用户提供的 revert 字符串添加进一步的信息（尚未实现）。

#### 额外的模型检查器设置

[Solidity 的内置模型检查器](https://docs.soliditylang.org/en/latest/smtchecker.html#tutorial) 是一个可以通过 `ModelChecker` 对象启用的选择模块。

参见 [编译器输入说明 `settings.modelChecker`](https://docs.soliditylang.org/en/latest/using-the-compiler.html#compiler-input-and-output-json-description) 和 [模型检查器的选项](https://docs.soliditylang.org/en/latest/smtchecker.html#smtchecker-options-and-tuning)。

该模块在 OSX 和 Linux 的 `solc` 发布二进制文件中可用。后者需要在系统中安装 z3 库版本[4.8.8, 4.8.14]（SO 版本 4.8）。

与上面的优化器设置类似，`model_checker` 的设置必须以其对应的配置文件为前缀。`[profile.default.model_checker]` 属于 `[profile.default]`。

```toml
## foundry.toml
[profile.default.model_checker]
contracts = { '/path/to/project/src/Contract.sol' = [ 'Contract' ] }
engine = 'chc'
timeout = 10000
targets = [ 'assert' ]
```

上面的字段是在使用模型检查器时推荐的。
设置哪个合约应该被验证是非常重要的，否则所有可用的合约都会被验证，这会消耗大量的时间。
推荐的引擎是 `chc`，但 `bmc` 和 `all`（同时运行）也可以接受。
设置适当的超时（以毫秒为单位）也很重要，因为给底层解析器的默认时间可能是不够的。
如果没有给出验证目标，将只检查断言。

模型检查器将在调用 `forge build` 时运行，如果有的话，将以警告的形式显示结果。

### 可选

#### Build 选项

`--names`
&nbsp;&nbsp;&nbsp;&nbsp;打印编译的合约名称。

`--sizes`
&nbsp;&nbsp;&nbsp;&nbsp;打印已编译的非测试合约大小，如果其中任何一个超过大小限制，则以代码 1 退出。

`--skip`
&nbsp;&nbsp;&nbsp;&nbsp;跳过编译非必要的合约目录，比如测试或脚本（使用 `--skip test`）。

`[PATHS]...`&nbsp;&nbsp;&nbsp;&nbsp;从指定路径构建源文件。

{{#include core-build-options.md}}

{{#include watch-options.md}}

{{#include common-options.md}}

### 例子

1. 构建项目：
    ```sh
    forge build
    ```

2. 使用 solc 0.6.0 构建项目：
    ```sh
    forge build --use solc:0.6.0
    ```

3. 用额外的 artifact 输出来构建项目：
    ```sh
    forge build --extra-output evm.assembly
    ```

4. 使用监听模式构建项目：
    ```sh
    forge build --watch
    ```

5. 从 `test/invariant` 目录和 `test/RegressionTest.sol` 构建源文件：
    ```sh
    forge build test/invariant test/RegressionTest.sol
    ```

### 另请参阅

[forge](./forge.md), [forge clean](./forge-clean.md), [forge inspect](./forge-inspect.md)

[sparse-mode]: ../config/solidity-compiler.md#sparse_mode
[ir-pipeline]: https://docs.soliditylang.org/en/latest/ir-breaking-changes.html
[output-desc]: https://docs.soliditylang.org/en/latest/using-the-compiler.html#compiler-api
