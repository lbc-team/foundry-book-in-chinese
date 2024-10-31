## `assumeNoRevert`

### 签名

```solidity
function assumeNoRevert() external;
```

### 描述

如果下一个调用发生回退，模糊测试器将丢弃当前的模糊输入并开始新的模糊运行。

如果达到最大拒绝次数，测试可能会失败。

您可以通过在 `foundry.toml` 文件中设置 [`fuzz.max_test_rejects`][max-test-rejects] 来配置拒绝阈值。

### 示例

对于需要在特定范围内的金额的函数：
```solidity
function doSomething(uint256 amount) public {
    require(amount > 100 ether && amount < 1_000 ether);
}
```
回退被丢弃，导致测试通过（如果达到最大拒绝次数则失败）：
```solidity
function testSomething(uint256 amount) public {
    vm.assumeNoRevert();
    target.doSomething(amount);
    // [PASS]
}
```