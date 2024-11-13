## 作弊码（Cheatcodes）

大多数时候，仅仅测试你的智能合约输出是不够的。 为了操纵区块链的状态，以及测试特定的 reverts 和事件 Events，Foundry 附带了一组作弊码（Cheatcodes)。

作弊码允许你更改区块号、你的身份（地址）等。 它们是通过在特别指定的地址上 （`0x7109709ECfa91a80626fF3989D68f67F5b1DD12D`）调用特定函数来调用的。

你可以通过 Forge 标准库的 `Test` 合约中提供的 `vm` 实例轻松访问作弊码。 Forge 标准库在以下 [部分](./forge-std.md) 中有更详细的解释。

让我们为验证只能由所有者调用的智能合约编写一个测试。

```solidity
{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:prelude}}

{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:contract}}

{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:contract_prelude}}

{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:simple_test}}
}
```

如果我们现在运行 `forge test`，我们将看到测试通过，因为 `OwnerUpOnlyTest` 是 `OwnerUpOnly` 的所有者（owner）。

```ignore
$ forge test
{{#include ../output/cheatcodes/forge-test-simple:output}}
```

让我们确保绝对不是所有者的人不能增加计数：

```solidity
{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:contract_prelude}}

        // ...

{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:test_fail}}
}
```

如果我们现在运行 `forge test`，我们将看到所有测试都通过了。

```ignore
$ forge test
{{#include ../output/cheatcodes/forge-test-cheatcodes:output}}
```

测试通过是因为 `prank` 作弊码将我们的身份更改为零地址再进行下一次调用 (`upOnly.increment()`)。 由于我们使用了 `testFail` 前缀，测试用例通过了，但是，使用 `testFail` 被认为是一种反模式(anti-pattern)，因为它没有告诉我们任何关于*为什么* `upOnly.increment()` 被 revert 的信息。

如果我们在启用跟踪的情况下再次运行测试，我们可以看到 revert 了正确的错误消息。

```ignore
$ forge test -vvvv --match-test testFail_IncrementAsNotOwner
{{#include ../output/cheatcodes/forge-test-cheatcodes-tracing:output}}
```

为了将来的确定性，让我们确保我们 revert 了，可以使用`expectRevert`作弊码来验证我们不是所有者的情况：

```solidity
{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:contract_prelude}}

     // ...

{{#include ../../projects/cheatcodes/test/OwnerUpOnly.t.sol:test_expectrevert}}
}
```

如果我们最后再一次运行 `forge test`，我们会看到测试仍然通过，但这次我们确信如果因为任何其他原因 revert 时，它将总是会失败。

```ignore
$ forge test
{{#include ../output/cheatcodes/forge-test-cheatcodes-expectrevert:output}}
```

另一个可能不那么直观的作弊码是 `expectEmit` 函数。 在查看 `expectEmit` 之前，我们需要了解什么是事件（Event）。

事件是合约的可继承成员。 当触发出事件时，参数存储在区块链上。 `indexed` 属性最多可以添加到事件三个参数中，以形成称为 “主题（topic）” 的数据结构。 主题允许用户搜索（过滤）区块链上的事件。

```solidity
{{#include ../../projects/cheatcodes/test/EmitContract.t.sol:all}}
```

当我们调用 `vm.expectEmit(true, true, false, true);` 时，我们想要检查下一个事件的第一个和第二个 `indexed` 主题。

`test_ExpectEmit()` 中预期的 `Transfer` 事件意味着我们期望 `from` 是 `address(this)`，而 `to` 是 `address(1337)`。 这与从 emitter.t() 发出的事件进行比较。

换句话说，我们正在检查来自 `emitter.t()` 的第一个主题是否等于 `address(this)` 。 `expectEmit` 中的第三个参数设置为 `false`，因为不需要检查 `Transfer` 事件中的第三个主题，因为只有两个主题。 即使我们设置为 `true` 也没关系。

`expectEmit` 中的第 4 个参数设置为 `true`，这意味着我们要检查 "non-indexed topics（非索引主题）"，也称为数据。

例如，我们希望来自 `test_ExpectEmit` 中预期事件的数据（即 `amount`）等于实际发出的事件中的数据。 换句话说，我们断言 `emitter.t()` 发出的 `amount ` 等于 `1337`。 如果 `expectEmit` 中的第四个参数设置为 `false`，我们将不会检查 `amount`。

换句话说，`test_ExpectEmit_DoNotCheckData` 是一个有效的测试用例，即使数量不同，因为我们不检查数据。

<br>

> 📚 **参考**
>
> 请参阅 [作弊码 参考](../cheatcodes/) 以获得所有可用作弊码的完整概述。