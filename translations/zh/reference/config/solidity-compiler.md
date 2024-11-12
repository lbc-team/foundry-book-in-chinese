## Solidity 编译器

与 Solidity 编译器的行为有关的配置。

**栏目**

- [General](#general)
- [Optimizer](#optimizer)
- [Model Checker](#model-checker)

### General

与 Solidity 编译器的行为有关的配置。

##### `remappings`

- 类型: 字符串数组 (remappings)
- 默认值: none
- 环境变量: `FOUNDRY_REMAPPINGS` 或 `DAPP_REMAPPINGS`

一个格式如下的重映射数组: `<name>=<target>`.

一个重映射 _重映射_ 让 Solidity 从不同的目录导入。例如，下面的重映射

```ignore
@openzeppelin/=node_modules/@openzeppelin/openzeppelin-contracts/
```

导入方式如

```solidity
import {Context} from "@openzeppelin/contracts/utils/Context.sol";
```

变成了

```solidity
import {Context} from "node_modules/@openzeppelin/openzeppelin-contracts/contracts/utils/Context.sol";
```

##### `auto_detect_remappings`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_AUTO_DETECT_REMAPPINGS` 或 `DAPP_AUTO_DETECT_REMAPPINGS`

如果启用，Foundry 将自动尝试通过扫描 `libs` 文件夹来自动检测重映射。

如果设置为 `false`，则只使用 `foundry.toml` 和 `remappings.txt` 中的重映射。

##### `allow_paths`

- 类型: array of strings (paths)
- 默认值: none
- 环境变量: `FOUNDRY_ALLOW_PATHS` 或 `DAPP_ALLOW_PATHS`

告诉 solc 允许从其他目录读取源文件。这主要适用于由 `pnpm` 或类似文件管理的复杂工作区。

另请参阅 [solc allowed-paths](https://docs.soliditylang.org/en/latest/path-resolution.html#allowed-paths)

##### `include_paths`

- 类型: array of strings (paths)
- 默认值: none
- 环境变量: `FOUNDRY_INCLUDE_PATHS` 或 `DAPP_INCLUDE_PATHS`

为默认导入回调提供一个额外的源代码目录。如果你想导入与你的主源码树相比位置不固定的合约，例如，使用软件包管理器安装的第三方库，请使用此选项。可以多次使用。只有当基本路径有一个非空值时才能使用。

另请参阅 [solc path resolution](https://docs.soliditylang.org/en/latest/path-resolution.html)

##### `libraries`

- 类型: array of strings (libraries)
- 默认值: none
- 环境变量: `FOUNDRY_LIBRARIES` 或 `DAPP_LIBRARIES`

一个要链接的库的数组，格式如下。`<file>:<lib>:<address>`，例如：`src/MyLibrary.sol:MyLibrary:0xfD88CeE74f7D78697775aBDAE53f9Da1559728E4`。

##### `solc_version`

- 类型: string (semver)
- 默认值: none
- 环境变量: `FOUNDRY_SOLC_VERSION` 或 `DAPP_SOLC_VERSION`

如果指定，则覆盖自动检测系统（更多内容见下文）并为项目使用单一的 Solidity 编译器版本。

只支持严格的版本（即 `0.8.11` 有效，但 `^0.8.0` 无效）。

##### `auto_detect_solc`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_AUTO_DETECT_SOLC` or `DAPP_AUTO_DETECT_SOLC`

如果启用，Foundry 将自动尝试解决适当的 Solidity 编译器版本来编译您的项目。

如果设置了 `solc_version`，这个键就会被忽略

##### `offline`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_OFFLINE` or `DAPP_OFFLINE`

如果启用，Foundry 将不会尝试下载任何丢失的 solc 版本。

如果 `offline` 和 `auto-detect-solc` 都被设置为 `true`，所需的 solc 版本将被自动检测，但任何缺失的版本将 _不会_ 被安装。

##### `ignored_warnings_from`

- 类型：字符串数组（文件路径）
- 默认值：无
- 环境变量：`FOUNDRY_IGNORED_WARNINGS_FROM` 或 `DAPP_IGNORED_WARNINGS_FROM`

一个文件路径数组，在编译过程中应忽略这些路径中的警告。当你有特定的文件目录产生已知警告，并希望在不影响其他警告的情况下抑制这些警告时，这非常有用。

数组中的每个条目应为一个目录或特定文件的路径。例如：

`ignored_warnings_from = ["path/to/warnings/file1.sol", "path/to/warnings/file2.sol"]`

此配置将导致编译器忽略来自指定路径的任何警告。

##### `ignored_error_codes`

- 类型: array of integers/strings
- 默认值: none for source, SPDX license identifiers and contract size for tests
- 环境变量: `FOUNDRY_IGNORED_ERROR_CODES` or `DAPP_IGNORED_ERROR_CODES`

Solidity 编译器错误代码的数组，以便在构建期间忽略，例如警告。

Valid values are:

- `license`: 1878
- `code-size`: 5574
- `func-mutability`: 2018
- `unused-var`: 2072
- `unused-param`: 5667
- `unused-return`: 9302
- `virtual-interfaces`: 5815
- `missing-receive-ether`: 3628
- `shadowing`: 2519
- `same-varname`: 8760
- `unnamed-return`: 6321
- `unreachable`: 5740
- `pragma-solidity`: 3420
- `constructor-visibility`: 2462
- `init-code-size`: 3860
- `transient-storage`: 2394
- `too-many-warnings`: 4591

##### `evm_version`

- 类型: string
- Default: paris
- 环境变量: `FOUNDRY_EVM_VERSION` or `DAPP_EVM_VERSION`

测试中使用的 EVM 版本。该值必须是 EVM 的硬分叉名称，如 `london`，`byzantium` 等等。

##### `revert_strings`

- 类型: string
- 默认值: default
- 环境变量: `FOUNDRY_REVERT_STRINGS` or `DAPP_REVERT_STRINGS`

有效值为：

- `default` 不注入编译器生成的 revert 字符串，而是保留用户提供的 revert 字符串。
- `strip` 删除所有的 revert 字符串（如果可能的话，即如果使用了字面量），以保持副作用。
- `debug` 为编译器生成的内部 revert 注入字符串，目前为ABI编码器 V1 和 V2 实现。
- `verboseDebug` 甚至为用户提供的 revert 字符串添加进一步的信息（尚未实现）。

##### `extra_output_files`

- 类型: array of strings
- 默认值: none
- 环境变量: N/A

来自 Solidity 编译器的额外输出，应该被写入 artifacts 目录中的文件。

有效值为：

- `metadata`: 在 artifacts 目录中写入 `metadata.json` 文件
- `ir`: 在 artifacts 目录中写入 `.ir` 文件
- `irOptimized`: 在 artifacts 目录中写入 `.iropt` 文件
- `ewasm`: 在 artifacts 目录中写入 `.ewasm` 文件
- `evm.assembly`: 在 artifacts 目录中写入 `.asm` 文件

##### `extra_output`

- 类型: array of strings
- 默认值: see below
- 环境变量: N/A

在合约 artifact 目录中的额外输出

以下数值总是被设置，因为它们是Forge所需要的：

```toml
extra_output = [
  "abi",
  "evm.bytecode",
  "evm.deployedBytecode",
  "evm.methodIdentifiers",
]
```

关于有效值的列表，请参见 [Solidity docs][output-desc]。

##### `bytecode_hash`

- 类型: string
- 默认值: ipfs
- 环境变量: `FOUNDRY_BYTECODE_HASH` or `DAPP_BYTECODE_HASH`

确定附加到字节码上的元数据哈希值的哈希方法。

有效值为：

- ipfs (default)
- bzzr1
- none

##### `sparse_mode`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_SPARSE_MODE` or `DAPP_SPARSE_MODE`

启用 [sparse mode](../forge/forge-build.md#sparse-mode-experimental) 进行构建。

### Optimizer

与 Solidity 优化器有关的配置。

##### `optimizer`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_OPTIMIZER` 或 `DAPP_OPTIMIZER`

是否要启用 Solidity 优化器。

##### `optimizer_runs`

- 类型: integer
- 默认值: 200
- 环境变量: `FOUNDRY_OPTIMIZER_RUNS` 或 `DAPP_OPTIMIZER_RUNS`

运行次数大致指定了在合约生命周期内，每个已部署代码的操作码将被执行的频率。这意味着它是在代码大小（部署成本）和代码执行成本（部署后的成本）之间的权衡参数。`optimizer_runs` 参数为 `1` 将生成短但昂贵的代码。相反，较大的 `optimizer_runs` 参数将生成更长但更节省 gas 的代码。该参数的最大值为 `2**32-1`。

一个常见的误解是该参数指定了优化器的迭代次数。这并不正确：优化器将始终运行尽可能多的次数，只要它仍然能够改善代码。

##### `via_ir`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_VIA_IR` 或 `DAPP_VIA_IR`

如果设置为 `true`，通过新的 IR 优化器改变编译管道.

##### `use_literal_content`

- 类型：布尔值
- 默认值：false

如果设置为 true，则编译将仅使用文字内容而不使用 URL。

##### `[optimizer_details]`

优化器细节部分用于调整 Solidity 优化器的行为方式。在此部分有几个可配置的值（每个都是布尔值）。

- `peephole`
- `inliner`
- `jumpdestRemover`
- `orderLiterals`
- `deduplicate`
- `cse`
- `constantOptimizer`
- `yul`

关于默认值，请参考 Solidity [编译器输入说明](https://docs.soliditylang.org/en/latest/using-the-compiler.html#compiler-input-and-output-json-description)。

##### `[optimizer_details.yul_details]`

优化器细节部分的 Yul 细节小节是用来调整新的 IR 优化器的行为方式的。有两个配置值：

- `stack_allocation`: 试图通过提前释放堆栈槽来改善堆栈槽的分配。
- `optimizer_steps`: 选择要应用的优化器步骤。

关于默认值，请参考 Solidity [编译器输入说明](https://docs.soliditylang.org/en/latest/using-the-compiler.html#compiler-input-and-output-json-description)。

> ℹ️ **注意**  
> 如果你在使用 `via_ir` 时遇到编译器错误，请明确启用传统的 `optimizer`，并将`optimizer_steps` 设为空字符串。

### Model checker

Solidity 模型检查器是一个内置的选择模块，可用于 OSX 和 Linux 的 Solidity 编译器中。在 [Solidity 编译器文档](https://docs.soliditylang.org/en/latest/smtchecker.html#tutorial) 中了解更多关于模型检查器的信息。

> ℹ️ **注意**  
> 模型检查器需要在 Linux 上的 `z3` 版本为 4.8.8 或 4.8.14。

模型检查器的设置是在配置的 `[model_checker]` 部分配置的

当调用 `forge build` 时，模型检查器将运行，任何发现将显示为警告。

以下是使用模型检查器时的推荐设置：

```toml
[profile.default.model_checker]
contracts = {'/path/to/project/src/Contract.sol' = ['Contract']}
engine = 'chc'
timeout = 10000
targets = ['assert']
```

设置哪个合约需要被验证是非常重要的，否则所有可用的合约都会被验证，这可能需要很长的时间。

推荐的引擎是 `chc`，但 `bmc` 和 `all` (同时运行) 也可以接受。

设置适当的超时（以毫秒为单位）也很重要，因为给底层编码器的默认时间可能是不够的。

如果没有给出验证目标，将只检查断言。

##### `[model_checker]`

在模型检查器部分有以下键。

###### `model_checker.contracts`

- 类型: table
- 默认值: all
- 环境变量: N/A

指定模型检查器分析哪些合约。

table 的键是源文件的路径，而值是要检查的合约名称的数组。

例如：

```toml
[profile.default.model_checker]
contracts = { "src/MyContracts.sol" = ["ContractA", "ContractB"] }
```

###### `model_checker.div_mod_with_slacks`

- Type: boolean
- Default: false
- Environment: N/A

设置如何对除法和取模运算进行编码。

有关更多信息，请参阅 [Solidity documentation](https://docs.soliditylang.org/en/latest/smtchecker.html#division-and-modulo-with-slack-variables)。

###### `model_checker.engine`

- 类型: string (see below)
- 默认值: all
- 环境变量: N/A

指定要运行的模型检查器引擎。有效值是：

- `chc`: 受约束 horn clauses 引擎
- `bmc`: 有界限的模型检查器引擎
- `all`: 同时运行两种引擎

请参阅 [Solidity 文档](https://docs.soliditylang.org/en/latest/smtchecker.html#model-checking-engines) 了解更多关于引擎信息


###### `model_checker.invariants`

- Type: array of strings
- Default: N/A
- Environment: N/A

设置模型检查器不变量。有效值为:

- `contract`: 合约不变量
- `reentrancy`: 重入属性

有关不变量的更多信息，请参阅 [Solidity documentation](https://docs.soliditylang.org/en/latest/smtchecker.html#reported-inferred-inductive-invariants)。

###### `model_checker.show_unproved`

- Type: boolean
- Default: false
- Environment: N/A

是否输出所有未证明的目标。

有关更多信息，请参阅 [Solidity documentation](https://docs.soliditylang.org/en/latest/smtchecker.html#unproved-targets)。

###### `model_checker.solvers`

- Type: array of strings
- Default: N/A
- Environment: N/A

设置模型检查器求解器。有效值为:

- `cvc4`
- `eld`: 自 v0.8.18 引入
- `smtlib2`
- `z3`

有关更多信息，请参阅 [Solidity documentation](https://docs.soliditylang.org/en/latest/smtchecker.html#smt-and-horn-solvers)。


###### `model_checker.timeout`

- 类型: number (milliseconds)
- 默认值: N/A
- 环境变量: N/A

设置底层模型检查器引擎的超时（以毫秒为单位）。

###### `model_checker.targets`

- 类型: array of strings
- 默认值: assert
- 环境变量: N/A

设置模型检查器的目标。有效值是：

- `assert`: 断言
- `underflow`: 算术下溢
- `overflow`: 算术溢出
- `divByZero`: 除以零
- `constantCondition`: 琐碎的条件和无法到达的代码
- `popEmptyArray`: 弹出一个空数组
- `outOfBounds`: 越界的数组/固定字节索引访问
- `default`: 以上所有（注意：不是Forge的默认值）
