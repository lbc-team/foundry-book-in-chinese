## `assume`

### 签名

```solidity
function assume(bool) external;
```

### 描述

如果布尔表达式结果为假，模糊测试器将丢弃当前的测试输入，并开始新的运行。

`assume`作弊码主要用于特定情况检查以避免减慢测试速度，因为广泛测试会花费很多事件并且可能会因为达到测试失败的次数上限而被终止。因此，建议在使用`assume`作弊码时进行有针对性的、较为精确的检查。

您可以在`foundry.toml`文件中通过[`fuzz.max_test_rejects`][max-test-rejects]参数设置阈值。

对于广泛的检查，例如检查 `uint256` 在特定范围内，您可以使用取模运算符或 Forge 标准库的 [`bound`][forge-std-bound] 方法来检查。

通过 `assume` 进行过滤的更多信息参见如下 [链接][filtering-guide].

### 示例

```solidity
// Good example of using assume
function testSomething(uint256 a) public {
    vm.assume(a != 1);
    require(a != 1);
    // [PASS]
}
```

```solidity
// In this case assume is not a great fit, so you should bound inputs manually
function testSomethingElse(uint256 a) public {
    a = bound(a, 100, 1e36);
    require(a >= 100 && a <= 1e36);
    // [PASS]
}
```

### 参见

Forge 标准库

[`bound`](../reference/forge-std/bound.md)

[max-test-rejects]: ../reference/config/testing.md#max_test_rejects
[forge-std-bound]: ../reference/forge-std/bound.md
[filtering-guide]: https://altsysrq.github.io/proptest-book/proptest/tutorial/filtering.html#filtering
