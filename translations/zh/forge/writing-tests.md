## 编写测试

测试是用 Solidity 编写的。 如果测试功能 revert，则测试失败，否则通过。

让我们回顾一下最常见的编写测试的方式，使用 [Forge 标准库(forge-std)](https://github.com/foundry-rs/forge-std) 的 `Test` 合约，这是使用 Forge 编写测试的首选方式。

在本节中，我们将使用 Forge Std 的 `Test` 合约中的函数复习基础知识，该合约本身是 [DSTest](https://github.com/dapphub/ds-test) 的超集。 [很快](./forge-std.md)您将学习如何使用 Forge 标准库中的更多高级内容 。

DSTest 提供基本的日志记录和断言功能。 要访问这些函数，请导入 `forge-std/Test.sol` 并继承自测试合约 `Test` ：

```solidity
{{#include ../../projects/writing_tests/test/Basic.t.sol:import}}
```

让我们检查一个基本测试：

```solidity
{{#include ../../projects/writing_tests/test/Basic.t.sol:all}}
```

Forge 在测试中使用以下关键字：

- `setUp`：在每个测试用例运行之前调用的可选函数
```solidity
{{#include ../../projects/writing_tests/test/Basic.t.sol:setUp}}
```
- `test`：以 `test` 为前缀的函数作为测试用例运行
```solidity
{{#include ../../projects/writing_tests/test/Basic.t.sol:testNumberIs42}}
```

- `testFail`: `test` 前缀的测试的反面 - 如果函数没有 revert，则测试失败
```solidity
{{#include ../../projects/writing_tests/test/Basic.t.sol:testFailSubtract43}}
```
一个好的实践是结合 [`expectRevert`](../cheatcodes/expect-revert.md) cheatcode 来使用 `test_Revert[If|When]_Condition` 模式（cheatcodes 在以下[部分](./cheatcodes.md)有更详细的解释）。此外，其他测试实践可以在 [Tutorials section](../tutorials/best-practices.md) 中找到。
现在，不再使用 `testFail`，您可以确切地知道发生了什么并且出现了哪些错误：

```solidity
{{#include ../../projects/writing_tests/test/Basic2.t.sol:testCannotSubtract43}}
```
<br>

测试合约部署到 `0xb4c79daB8f259C7Aee6E5b2Aa729821864227e84`。 如果您在测试中部署合约，则
`0xb4c...7e84` 将是它的部署者。 如果在测试中部署的合约向其部署者授予特殊权限，
例如 `Ownable.sol` 的 `onlyOwner` 修饰符，那么测试合约 `0xb4c...7e84` 将具有这些权限。

> ⚠️ **注意**
>
> 测试函数必须具有`external`或`public`可见性。 声明为`internal`或
> `private` 不会被 Forge 选中，即使它们以 `test` 为前缀。

### 共享设置

可以通过创建辅助抽象合约并在测试合约中继承它们来使用共享设置：

```solidity
abstract contract HelperContract {
    address constant IMPORTANT_ADDRESS = 0x543d...;
    SomeContract someContract;
    constructor() {...}
}

contract MyContractTest is Test, HelperContract {
    function setUp() public {
        someContract = new SomeContract(0, IMPORTANT_ADDRESS);
        ...
    }
}

contract MyOtherContractTest is Test, HelperContract {
    function setUp() public {
        someContract = new SomeContract(1000, IMPORTANT_ADDRESS);
        ...
    }
}
```

<br>

> 💡 **提示**
>
> 可使用 [`getCode`](../cheatcodes/get-code.md) 作弊码部署具有不兼容 Solidity 版本的合约。