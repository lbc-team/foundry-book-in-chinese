## 调试器 Debugger

Forge 附带一个交互式调试器 Debugger。

调试器 Debugger 可在 [`forge debug`](../reference/forge/forge-debug.md) 和 [`forge test`](../reference/forge/forge-test.md) 上访问。

使用 `forge test`:

```sh
$ forge test --debug $FUNC
```

其中 `$FUNC` 是您要调试的函数的签名。 例如：

```sh
$ forge test --debug "testSomething()"
```

如果您有多个具有相同函数名称的合约，则需要使用 `--match-path` 和 `--match-contract` 将匹配函数限制为只有一种情况。

如果匹配测试是模糊测试，调试器 Debugger 将打开第一个失败的模糊场景，或者最后一个成功的场景，以先到者为准。

使用 `forge debug`：

```sh
$ forge debug --debug $FILE --sig $FUNC
```

其中 `$FILE` 是您要调试的合约的路径，`$FUNC` 是您要调试的函数的签名。 例如：

```sh
$ forge debug --debug src/SomeContract.sol --sig "myFunc(uint256,string)" 123 "你好"
```

您还可以使用 `--sig` 而不是函数签名来指定原始调用数据。

如果您的源文件包含多个合约，请使用`--target-contract`标志指定要调试的合约。


### 调试器布局

![An image of the debugger UI](../images/debugger.png)


### 导航

当调试器 Debugger 运行时，您会看到一个分为四个象限的终端：

- **Quadrant 1**:调试会话中的操作码，当前操作码突出显示。 此外，还会显示当前账户地址、程序计数器和累计 Gas 用量
- **Quadrant 2**：当前栈，以及栈的大小
- **Quadrant 3**:源视图
- **Quadrant 4**:EVM 的当前内存

在逐步执行代码时，您会注意到堆栈和内存中的单词有时会改变颜色。

对于内存：

- **Red words** 即将被当前操作码写入
- **Green words** 被之前的操作码写入
- **Cyan words** 正在被当前操作码读取

对于堆栈，**cyan words**正在被当前操作码读取或弹出。

### 导航

### General

- <kbd>q</kbd>: Quit the debugger

### Navigating calls

- <kbd>0-9</kbd> + <kbd>k</kbd>：向后移动数次（或者用鼠标向上滚动）
- <kbd>0-9</kbd> + <kbd>j</kbd>：向前走几步（或者用鼠标向下滚动）
- <kbd>g</kbd>: 移动到事务的开头
- <kbd>G</kbd>：移至事务末尾
- <kbd>c</kbd>：移至上一个调用类型指令（即 [`CALL`][op-call]、[`STATICCALL`][op-staticcall]、[`DELEGATECALL`][op- delegatecall] 和 [`CALLCODE`][op-callcode]）。
- <kbd>C</kbd>: 移动到下一个调用类型指令
- <kbd>a</kbd>: 移动到上一个 [`JUMP`][op-jump] 或 [`JUMPI`][op-jumpi] 指令
- <kbd>s</kbd>: 移动到下一个 [`JUMPDEST`][op-jumpdest] 指令

### Navigating memory
- <kbd>Ctrl</kbd> + <kbd>j</kbd>：向下滚动内存视图
- <kbd>Ctrl</kbd> + <kbd>k</kbd>：向上滚动内存视图
- <kbd>m</kbd>: 将内存显示为 UTF8

### Navigating the stack
- <kbd>J</kbd>：向下滚动堆栈视图
- <kbd>K</kbd>：向上滚动堆栈视图
- <kbd>t</kbd>: 在堆栈上显示标签以查看当前操作将消耗哪些项

[op-call]: https://www.evm.codes/#f1
[op-staticcall]: https://www.evm.codes/#fa
[op-delegatecall]: https://www.evm.codes/#f4
[op-callcode]: https://www.evm.codes/#f2
[op-jumpdest]: https://www.evm.codes/#5b
[op-jump]: https://www.evm.codes/#f1
[op-jumpi]: https://www.evm.codes/#57
