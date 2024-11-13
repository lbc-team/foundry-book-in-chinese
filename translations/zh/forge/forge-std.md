## Forge 标准库概览

Forge Standard Library（简称 Forge Std）是一个有用的合约集合，可以让编写测试更简单、更快速、更人性化。

使用 Forge Std 是使用 Foundry 编写测试的首选方式。

它提供了开始编写测试所需的所有基本功能：

- `Vm.sol`：最新的作弊码接口
- `console.sol` 和 `console2.sol`：Hardhat 风格的日志记录功能
- `Script.sol`：[Solidity 脚本](../tutorials/solidity-scripting.md) 的基本实用程序
- `Test.sol`：DSTest 的超集，包含标准库、作弊码实例 (`vm`) 和 Hardhat 控制台

简单的导入 `Test.sol` 并在测试合约中继承 `Test` ：

```solidity
import {Test} from "forge-std/Test.sol";

contract ContractTest is Test { ...
```

现在你可以：

```solidity
// Access Hevm via the `vm` instance
vm.startPrank(alice);

// Assert and log using Dappsys Test
assertEq(dai.balanceOf(alice), 10000e18);

// Log with the Hardhat `console` (`console2`)
console.log(alice.balance);

// Use anything from the Forge Std std-libraries
deal(address(dai), alice, 10000e18);
```


要单独导入 `Vm`  接口或 `console` 库：

```solidity
import {Vm} from "forge-std/Vm.sol";
```

```solidity
import {console} from "forge-std/console.sol";
```

**注意：** `console2.sol` 包含 `console.sol` 的补丁，允许 Forge 解码控制台调用的跟踪，但它与 `Hardhat` 不兼容。

```solidity
import {console2} from "forge-std/console2.sol";
```

### 标准库

Forge Std 目前由六个标准库组成。

#### Std Logs

Std Logs 扩展了 [`DSTest`](../reference/ds-test.md#logging) 库中的日志记录事件。

#### Std Assertions

Std Assertions 扩展了 [`DSTest`](../reference/ds-test.md#asserting) 库中的断言函数。

#### Std Cheats

Std Cheats 是 Forge 作弊码的包装器，使它们更安全地使用和改进 DX。

你可以通过简单地在测试合约中调用它们来访问 Std Cheats，就像你调用任何其他内部函数一样：

```solidity
// set up a prank as Alice with 100 ETH balance
hoax(alice, 100 ether);
```

#### Std Errors

Std Errors 提供围绕常见内部 Solidity 错误 errors 和回退 reverts 的包装器。

Std Errors 与 [`expectRevert`](../cheatcodes/expect-revert.md) 作弊码结合使用最有用，因为你不需要自己记住内部 Solidity panic codes。 请注意，你必须通过 `stdError` 访问它们，因为这是一个库。

```solidity
// expect an arithmetic error on the next call (e.g. underflow)
vm.expectRevert(stdError.arithmeticError);
```

#### Std Storage

Std Storage 使操作合约存储变得容易。 它可以找到并写入与特定变量关联的存储槽。

`Test` 合约已经提供了一个 `StdStorage` 实例 `stdstore`，你可以通过它访问任何标准存储功能。 请注意，你必须先在测试合约中添加语句：`using stdStorage for StdStorage`。

```solidity
// find the variable `score` in the contract `game`
// and change its value to 10
stdstore
    .target(address(game))
    .sig(game.score.selector)
    .checked_write(10);
```

#### Std Math

Std Math 是一个库，其中包含 Solidity 中未提供的有用的数学函数。

请注意，你必须通过 `stdMath` 访问它们，因为这是一个库。

```solidity
// get the absolute value of -10
uint256 ten = stdMath.abs(-10)
```

<br>

> 📚 **参考**
>
> 有关 Forge 标准库的完整概述，请参阅 [Forge 标准库参考](../reference/forge-std/)。