## 模糊测试(Fuzz Testing)

Forge 支持基于属性的测试。

基于属性的测试是一种测试一般行为而不是孤立场景的方法。

让我们通过编写单元测试来检查这意味着什么，找到我们正在测试的一般属性，并将其转换为基于属性的测试：

```solidity
{{#include ../../projects/fuzz_testing/test/Safe.t.sol.1:all}}
```

运行测试，我们看到它通过了：

```sh
{{#include ../output/fuzz_testing/forge-test-no-fuzz:all}}
```

这个单元测试_确实测试_我们可以从我们的保险箱（Safe 合约）中取出以太币。 但是，谁能说它适用于所有金额，而不仅仅是 1 个以太币？

这里的一般性质是：给定一个安全的余额，当我们提取时，我们应该得到保险箱里的所有的资金。

Forge 将运行任何至少采用一个参数的测试作为基于属性的测试，所以让我们重写：

```solidity
{{#include ../../projects/fuzz_testing/test/Safe.t.sol.2:contract_prelude}}
    // ...

{{#include ../../projects/fuzz_testing/test/Safe.t.sol.2:test}}
}
```

如果我们现在运行测试，我们可以看到 Forge 运行基于属性的测试，但它因 amount 的高值而失败：

```sh
$ forge test
{{#include ../output/fuzz_testing/forge-test-fail-fuzz:output}}
```

给测试合约的默认以太币数量是 `2**96 wei`（在 DappTools 中），所以我们必须将 amount 类型限制为 `uint96` ，以确保我们不会尝试发送超过`uint96`的值， 我们使用参数：

```solidity
{{#include ../../projects/fuzz_testing/test/Safe.t.sol.3:signature}}
```

现在它通过了：

```sh
{{#include ../output/fuzz_testing/forge-test-success-fuzz:all}}
```

您可能希望使用 [`assume`](../cheatcodes/assume.md) 作弊码排除某些情况。 在这些情况下，模糊器 fuzzer 将丢弃输入并开始运行新的模糊测试：

```solidity
function testFuzz_Withdraw(uint96 amount) public {
    vm.assume(amount > 0.1 ether);
    // snip
}
```

有多种方法可以运行基于属性的测试，特别是参数测试和模糊测试。 Forge 仅支持模糊测试。

### 解读结果

您可能已经注意到，与单元测试相比，模糊测试的总结略有不同：

- "runs" 是指模糊器 fuzzer 测试的场景数量。 默认情况下，模糊器 fuzzer 将生成 256 个场景，但用户可以设置此参数以及其他测试执行参数。有关模糊测试器配置详细信息，请参阅 [`这里`](#configuring-fuzz-test-execution)。
- “μ”（希腊字母 mu）是所有模糊运行中使用的平均 Gas
- “~”（波浪号）是所有模糊运行中使用的中值 Gas

### 配置模糊测试执行

模糊测试执行受用户通过 Forge 配置原语控制的参数的约束。配置可以全局应用，也可以基于每个测试进行应用。有关此主题的详细信息，请参阅
 📚 [`全局配置`](../reference/config/testing.md) 和 📚 [`内联配置`](../reference/config/inline-test-config.md)。