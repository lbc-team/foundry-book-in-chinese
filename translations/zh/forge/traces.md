## 理解跟踪(Traces)

Forge 可以为失败的测试（`-vvv`）或所有测试（`-vvvv`）生成跟踪(Traces)。

跟踪遵循相同的通用格式：

```ignore
  [<Gas Usage>] <Contract>::<Function>(<Parameters>)
    ├─ [<Gas Usage>] <Contract>::<Function>(<Parameters>)
    │   └─ ← <Return Value>
    └─ ← <Return Value>
```

每个跟踪可以有更多的子跟踪（subtraces），每个 subtraces 表示对合约的调用和返回值。

如果你的终端支持颜色，跟踪也会有多种颜色：

- **绿色**：对于不会 revert 的调用
- **红色**：用于有 revert 的调用
- **蓝色**：用于调用作弊码
- **青色**：用于触发日志
- **黄色**：用于合约部署

Gas 使用量（标在方括号中）显示整个函数调用 Gas。 但是，你可能会注意到，有时一条 trace 的 Gas 使用量与其所有 subtraces 的 Gas 使用量和并不完全匹配：

```ignore
  [24661] OwnerUpOnlyTest::testIncrementAsOwner()
    ├─ [2262] OwnerUpOnly::count()
    │   └─ ← 0
    ├─ [20398] OwnerUpOnly::increment()
    │   └─ ← ()
    ├─ [262] OwnerUpOnly::count()
    │   └─ ← 1
    └─ ← ()
```

下落不明的 Gas 是由于调用之间发生的一些额外操作，例如算术和存储读/写。

Forge 将尝试解码尽可能多的签名和值，但有时这是不可能的。 在这些情况下，Traces 将出现如下显示：

```ignore
  [<Gas Usage>] <Address>::<Calldata>
    └─ ← <Return Data>
```

有些追踪信息可能乍一看很难理解。包括以下几点：

- 简写 `OOG` 代表“Out Of Gas”（用尽 gas）。
- 缩写 `EOF` 代表“Ethereum Object Format”（以太坊对象格式），它为 EVM 字节码引入了一种可扩展且版本化的容器格式。欲了解更多信息，请阅读[这里](https://evmobjectformat.org/) 。
- `NotActivated` 表示功能或操作码未激活。某些版本的 EVM 仅支持特定的操作码。你可能需要使用更近期的版本，并使用 `--evm_version` 标志。例如，`PUSH0` 操作码仅自从 [上海](https://www.evm.codes/?fork=shanghai) 硬分叉后可用。
- `InvalidFEOpcode` 表示在执行过程中遇到了未定义的字节码值。EVM 捕获未知字节码并返回值为 `0xFE` 的 `INVALID` 操作码。你可以在[这里](https://www.evm.codes/#fe)找到更多信息。

若要深入了解各种追踪信息，可以探索 [revm 源代码](https://github.com/bluealloy/revm/blob/main/crates/interpreter/src/instruction_result.rs)。