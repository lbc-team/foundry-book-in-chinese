## 内联测试配置
Foundry 用户可以使用 `foundry.toml` 中的 ENV 变量和配置语句来指定整体测试配置。请查看[`Testing reference`][Testing Reference]以获取详细描述。

尽管这在一般情况下可能有效，但某些测试可能需要对其配置进行更精细的控制。出于这样的原因，Forge 提供了一种方式来为不变和模糊测试场景指定每个测试的配置。

用户可以直接在 Solidity 注释中进行内联测试配置语句。这将影响 `forge test` 命令对特定测试实例的行为，如下例所示。

```solidity
contract MyTest is Test {
  /// forge-config: default.fuzz.runs = 100
  /// forge-config: ci.fuzz.runs = 500
  function test_SimpleFuzzTest(uint256 x) public {
    // --- snip ---
  }
}
```

这里我们要求对 `default` 和 `ci` 配置分别运行我们的 fuzzer `100` 和 `500` 次。有趣的是，这将覆盖全局级别现有的任何模糊 `runs` 设置。所有其他配置将从全局上下文中继承，使其作为所有可能配置的备份。

### 块注释
内联测试配置也可以在块注释中表示，如下例所示。

```solidity
contract MyTest is Test {
  /**
   * forge-config: default.fuzz.runs = 1024
   * forge-config: default.fuzz.max-test-rejects = 500
   */
  function test_SimpleFuzzTest(uint256 x) public {
    // --- snip ---
  }
}
```

### 内联模糊配置
用户可以指定表中描述的配置。每个语句必须具有形式为 `forge-config: ${PROFILE}.fuzz.` 的前缀。

| 参数 | 类型 | 描述 |
|-|-|-|
|`runs`|整数|执行此特定测试用例的模糊运行次数。[`参考`][testing]|
|`max-test-rejects`|整数|在整个测试中可能被拒绝的组合输入的最大数量。[`参考`][Max test rejects]|

模糊配置示例
```solidity
contract MyFuzzTest is Test {
  /// forge-config: default.fuzz.runs = 100
  /// forge-config: default.fuzz.max-test-rejects = 2
  function test_InlineConfig(uint256 x) public {
    // --- snip ---
  }
}
```

### 内联不变配置
用户可以指定表中描述的配置。每个语句必须具有形式为 `forge-config: ${PROFILE}.invariant.` 的前缀。

| 参数 | 类型 | 描述 |
|-|-|-|
|`runs`|整数|执行此特定测试用例的不变运行次数。[`参考`][Invariant runs]|
|`depth`|整数|尝试在一个运行中执行的调用数量以跳出不变性。[`参考`][Invariant depth]|
|`fail-on-revert`|布尔值|如果发生 revert，则不变性模糊失败。[`参考`][Fail on revert]|
|`call-override`|布尔值|在运行不变性测试时覆盖不安全的外部调用。[`参考`][Invariant call override]|

不变配置示例
```solidity
contract MyInvariantTest is Test {
  /// forge-config: default.invariant.runs = 100
  /// forge-config: default.invariant.depth = 2
  /// forge-config: default.invariant.fail-on-revert = false
  /// forge-config: default.invariant.call-override = true
  function invariant_InlineConfig() public {
    // --- snip ---
  }
}
```





[Testing Reference]: ./testing.md
[testing]: ./testing.md#runs
[Max test rejects]: ./testing.md#max_test_rejects
[Invariant runs]: ./testing.md#runs-1
[Invariant depth]: ./testing.md#depth
[Fail on revert]: ./testing.md#fail_on_revert
[Invariant call override]: ./testing.md#call_override