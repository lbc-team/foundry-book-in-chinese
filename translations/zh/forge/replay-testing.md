## 重放失败

Forge 支持通过将最后一次测试运行的失败持久化到磁盘上，逐步重放这些失败。
### 重新运行失败
可以使用 `--rerun` 选项来省略成功的测试，仅重放记录的失败：
```bash
forge test --rerun
``` 
失败的测试会写入 `~/.foundry/cache/test-failures` 文件。每次执行 `forge test` 时，该文件都会更新，因此它反映了上次运行的失败。

### 模糊测试失败
Forge 保存所有模糊测试的反例，并在开始新的测试活动之前重放它们（这样做是为了确保没有引入回归）。
在多次运行中遇到的模糊测试失败默认保存在 `~/.foundry/cache/fuzz/failures` 文件中。该文件的内容不会被后续的测试运行替换，而是将新记录添加到现有条目中。

用于持久化和重新运行模糊测试失败的默认文件可以在 foundry.toml 中更改：
```toml
[fuzz]
failure_persist_file="/tests/failures.txt"
```

或通过使用内联配置
```Solidity
/// forge-config: default.fuzz.failure-persist-file = /tests/failures.txt
```

### 不变性测试失败
不变性测试的失败会被保存并在开始新的测试活动之前重放，类似于模糊测试。不同之处在于，失败的序列会保存在单独的文件中，具体路径为 `~/.foundry/cache/invariant/failures/{TEST_SUITE_NAME}/{INVARIANT_NAME}`。该文件的内容仅在找到不同的反例时才会被替换。

用于持久化不变性测试失败的默认目录可以在 foundry.toml 中更改：
```toml
[invariant]
failure_persist_dir="/tests/dir"
```

或通过使用内联配置
```Solidity
/// forge-config: default.invariant.failure-persist-dir = /tests/dir
```

### 移除持久化失败
要忽略已保存的失败并开始一个干净的测试活动，只需删除持久化的文件或运行 [`forge clean`](../reference/forge/forge-clean.md)（删除所有构建工件和缓存目录）。