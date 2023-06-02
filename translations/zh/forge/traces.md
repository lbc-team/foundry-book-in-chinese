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

如果您的终端支持颜色，跟踪也会有多种颜色：

- **绿色**：对于不会 revert 的调用
- **红色**：用于有 revert 的调用
- **蓝色**：用于调用作弊码
- **青色**：用于触发日志
- **黄色**：用于合约部署

Gas 使用量（标在方括号中）显示整个函数调用 Gas。 但是，您可能会注意到，有时一条 trace 的 Gas 使用量与其所有 subtraces 的 Gas 使用量和并不完全匹配：

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
