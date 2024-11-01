## `expectRevert`

### 签名

```solidity
function expectRevert() external;
```

```solidity
function expectRevert(bytes4 message) external;
```

```solidity
function expectRevert(bytes calldata message) external;
```

```solidity
function expectPartialRevert(bytes4 message) external;
```

### 描述

如果 **下一次调用** 没有以期望的数据 `message` 回滚，那么 `expectRevert` 会执行回滚。

调用 `expectRevert` 后，在回滚调用之前对其他作弊码的调用将被忽略。

这意味着，例如，我们可以在回滚调用之前立即调用 [`prank`](./prank.md)。

`expectRevert` 有 3 个签名：

- **没有参数**：断言下一次调用会回滚，无论消息是什么。
- **带有`bytes4`**：断言下一次调用会以指定的 4 个字节回滚并且完全匹配回滚数据。
- **带有`bytes`**：断言下一次调用会以指定的字节回滚。

`expectPartialRevert` 有一个签名：
- **`bytes4`**：断言下一个调用会回退，并且指定的 4 字节与回滚数据的前 4 字节匹配。

> ℹ️  **注意：**
> 
> 自定义错误可能包含在测试环境中有时难以计算的参数，或者它们可能与当前测试无关（例如，在第三方合约的内部函数中计算的值）。在这种情况下，可以使用 `expectPartialRevert` 来忽略参数，仅匹配自定义错误的选择器。例如，测试一个使用 `WrongNumber(uint256 number)` 自定义错误回退的函数：
> ```solidity
> function count() public {
>     revert WrongNumber(0);
> }
> ```
> should pass when using `expectPartialRevert`:
> ```solidity
> vm.expectPartialRevert(Counter.WrongNumber.selector);
> counter.count();
> ```
> but fails if exact match expected:
> ```solidity
> vm.expectRevert(Counter.WrongNumber.selector);
> counter.count();
> ```

> ⚠️ **注意：与低级调用一起使用**
>
> 通常，成功的调用返回 `true`（以及任何返回数据），而回滚的调用返回 `false`。
>
> Solidity 编译器将插入检查，确保调用成功，并在失败时回滚。
>
> 在低级调用中，`expectRevert` 作弊码通过使低级调用返回的布尔值 `status` 对应于 `expectRevert` 是否成功，而不是低级调用是否成功。因此，`status` 为 false 对应于作弊码失败。
>
> 除此之外，`expectRevert` 还会在低级调用中修改返回数据，并且无法使用。
>
> 请参阅以下示例。为了清晰起见，`status` 已重命名为 `revertsAsExpected`：

> ```solidity
> function testLowLevelCallRevert() public {
>     vm.expectRevert(bytes("error message"));
>     (bool revertsAsExpected, ) = address(myContract).call(myCalldata);
>     assertTrue(revertsAsExpected, "expectRevert: call did not revert");
> }
> ```

### 示例

要使用 `expectRevert` 与 `string`，请将其作为字符串文字传递。

```solidity
vm.expectRevert("error message");
```

要使用不带参数的自定义 [错误类型][error-type]与`expectRevert`，请使用其选择器。

```solidity
vm.expectRevert(CustomError.selector);
```

要使用带参数的自定义 [错误类型][error-type]与`expectRevert`，请对错误类型进行 ABI 编码。

```solidity
vm.expectRevert(
    abi.encodeWithSelector(CustomError.selector, 1, 2)
);
```

如果您需要断言函数回滚_没有_消息，则可以使用`expectRevert(bytes(""))`。

```solidity
function testExpectRevertNoReason() public {
    Reverter reverter = new Reverter();
    vm.expectRevert(bytes(""));
    reverter.revertWithoutReason();
}
```

当出现 EVM 错误时，例如当交易消耗超过区块的燃气限制时，会发生无消息的回滚。

如果您需要断言函数以四个字符消息，例如 `AAAA` 回滚，可以这样做：

```solidity
function testFourLetterMessage() public {
    vm.expectRevert(bytes("AAAA"));
}
```

如果使用 `expectRevert("AAAA")`，编译器会抛出错误，因为它不知道使用哪个重载。

最后，您还可以在单个测试中进行多个 `expectRevert()` 检查。

```solidity
function testMultipleExpectReverts() public {
    vm.expectRevert("INVALID_AMOUNT");
    vault.send(user, 0);

    vm.expectRevert("INVALID_ADDRESS");
    vault.send(address(0), 200);
}
```

要在 `expectPartialRevert` 中使用自定义的[错误类型][error-type] ，请使用它的选择器。

```solidity
vm.expectRevert(CustomError.selector);
```

### 参见

Forge 标准库

[Std Errors](../reference/forge-std/std-errors.md)

[error-type]: https://docs.soliditylang.org/en/v0.8.11/contracts.html#errors