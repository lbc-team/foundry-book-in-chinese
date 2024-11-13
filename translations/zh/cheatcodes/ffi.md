## `ffi`

### 签名

```solidity
functionffi(string[] calldata) external returns (bytes memory);
```

### 描述

如果启用了 [`ffi`](../reference/config/testing.md#ffi)，则调用任意命令。

通常建议将此作弊码作为最后的手段使用，并且不要默认启用它，因为可以更改项目测试的任何人都可以在运行测试的设备上执行任意命令。

### 提示

- 默认情况下，`ffi` 作弊码假定命令的输出是十六进制编码的值（例如，ABI 编码值的十六进制字符串）。如果十六进制解码失败，它将返回输出作为 UTF8 字节，你可以将其转换为字符串。
- 确保输出不包括 `\n` 换行符。（例如，在 Rust 中使用 `print!` 而不是 `println!`）
- 请记住，脚本将从项目的顶层目录执行，而不是在 `test` 目录内执行
- 确保输入数组不包含空元素。它们将被脚本作为输入处理，而不是空格
- 使用作弊码 `toString` 可以轻松将任意数据转换为字符串，以便你可以将它们作为命令行参数传递

### 例子

ABI 编码输出

```solidity
string[] memory inputs = new string[](3);
inputs[0] = "echo";
inputs[1] = "-n";
// ABI encoded "gm", as a hex string
inputs[2] = "0x00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000002676d000000000000000000000000000000000000000000000000000000000000";

bytes memory res = vm.ffi(inputs);
string memory output = abi.decode(res, (string));
assertEq(output, "gm");
```

UTF8 字符串输出

```solidity
string[] memory inputs = new string[](3);
inputs[0] = "echo";
inputs[1] = "-n";
inputs[2] = "gm";

bytes memory res = vm.ffi(inputs);
assertEq(string(res), "gm");
```