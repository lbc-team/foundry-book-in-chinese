## `assume`

### 签名

```solidity
function assume(bool) external;
```

### 描述

如果布尔表达式计算结果为 false，则模糊器将丢弃当前的模糊输入并开始新的模糊运行。

`assume` 作弊码主要用于非常狭窄的检查。
广泛的检查会减慢测试速度，因为找到有效值需要一段时间，如果达到最大拒绝次数，测试可能会失败。

你可以通过在你的 `foundry.toml` 文件中设置 [`fuzz.max_test_rejects`][max-test-rejects] 来配置拒绝阈值。

对于广泛的检查，比如确保 `uint256` 落在某个范围内，你可以使用取模运算符或 Forge Standard 的 [`bound`][forge-std-bound] 方法来限制你的输入。

有关通过 `assume` 进行过滤的更多信息，请参见[这里][filtering-guide]。

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