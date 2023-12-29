## Console Logging

- 与 Hardhat 的控制台功能类似。
- 你可以在调用和事务中使用它。它可以与视图函数一起使用，但不能在纯函数中使用。
- 无论调用或交易失败还是成功，它总是有效的。
- 要使用它，你需要导入 forge-std/console.sol。
- 你可以用以下类型的任何顺序的最多 4 个参数调用 console.log：
    - `uint`
    - `string`
    - `bool`
    - `address`
- 还有上述类型的单参数 API，另外还有 bytes, bytes1...直到 bytes32。
    - `console.logInt(int i)`
    - `console.logUint(uint i)`
    - `console.logString(string memory s)`
    - `console.logBool(bool b)`
    - `console.logAddress(address a)`
    - `console.logBytes(bytes memory b)`
    - `console.logBytes1(bytes1 b)`
    - `console.logBytes2(bytes2 b)`
    - ...
    - `console.logBytes32(bytes32 b)`
- console.log 实现了与 Hardhat 的 console.log 中相同的格式化选项。
    - 例子：`console.log("Changing owner from %s to %s", currentOwner, newOwner)`
- console.log 是用标准的 Solidity 实现的，它兼容 Anvil 和Hardhat 网络。
- console.log 调用可以在其他网络中运行，如 mainnet、kovan、ropsten 等。它们在这些网络中什么都不做，但确实花费了极少的 Gas。


### `console.log(format[,...args])`
`console.log()` 方法打印一个格式化的字符串，使用第一个参数作为类似 printf 的格式字符串，其中可以包含零个或多个格式指定符。每个指定符被替换成相应参数的转换值。支持的指定器有：

- `%s`: 字符串将被用来把所有的值转换为人类可读的字符串。`uint256`、`int256` 和 `bytes` 值将被转换为其 `0x` 十六进制编码值。
- `%d`: 数字将被用来把所有的值转换为人类可读的字符串。这与 `%s`相同。
- `%i`: 与 `%d` 的工作方式相同。
- `%e`: 数字的指数表示。适用于 `uint256` 和 `int256` 类型。
- `%x`: 数字的十六进制表示。适用于 `uint256` 和 `int256` 类型。
- `%o`: 对象。一个对象的字符串表示，具有通用的 JavaScript 风格的对象格式。对于 Solidity 类型，这基本上是用单引号包围了值的字符串表示。
- `%%`: 单一百分号（'%'）。这并不是一个参数。
- 返回：`<string>` 格式化的字符串

如果一个指定符没有相应的参数，它就不会被替换。
```solidity
console.log("%s:%s", "foo");
// Returns: "foo:%s"
```

不属于格式化字符串的值被格式化为人类可读的字符串表示。

如果传递给 console.log() 方法的参数多于指定的数量，额外的参数将被串联到返回的字符串中，用空格分隔：
```solidity
console.log("%s:%s", "foo", "bar", "baz");
// Returns: "foo:bar baz"
```

如果只有一个参数被传递给 console.log()，它将被原样返回，没有任何格式化。
```solidity
console.log("%% %s");
// Returns: "%% %s"
```

在大多数情况下应该使用字符串格式指定器（`%s`），除非需要其他格式指定器的特定功能。
