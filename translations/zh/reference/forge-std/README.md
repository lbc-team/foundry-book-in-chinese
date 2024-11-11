## Forge 标准库 参考

Forge 标准库（简称 Forge Std）是一个有用的合约的集合，使编写测试更容易、更快、更人性化。

使用 Forge Std 是使用 Foundry 编写测试的首选方式。

包含的内容：

- `Vm.sol`: 最新的 [作弊码接口](../../cheatcodes/#cheatcodes-interface)

    ```solidity
    import {Vm} from "forge-std/Vm.sol";
    ```

- [`console.sol`](./console-log.md) 和 `console2.sol`: Hardhat 样式的日志功能

    ```solidity
    import {console} from "forge-std/console.sol";
    ```

    **注意：** `console2.sol` 包含 `console.sol` 的补丁，允许Forge 解码对控制台的调用追踪，但它与 Hardhat 不兼容。

    ```solidity
    import {console2} from "forge-std/console2.sol";
    ```

- `Script.sol`: 用于 [Solidity scripting](../../tutorials/solidity-scripting.md) 的基础工具类。

    ```solidity
    import {Script} from "forge-std/Script.sol";
    ```

- `Test.sol`: 完整的 Forge Std 体验 (更多细节 [如下](#forge-stds-test))

    ```solidity
    import {Test} from "forge-std/Test.sol";
    ```

### Forge Std's `Test`

`Test.sol` 中的 `Test` 合约提供了你开始编写测试所需的所有基本功能。

只需导入 `Test.sol` 并在你的测试合约中继承 `Test`。

```solidity
import {Test} from "forge-std/Test.sol";

contract ContractTest is Test { ...
```

包含的内容：

- 标准库
  - [Std Logs](./std-logs.md): 在 DSTest 库的日志事件的基础上进行扩展。
  - [Std Assertions](./std-assertions.md): 在 DSTest 库的断言功能的基础上进行扩展。
  - [Std Cheats](./std-cheats.md): 围绕 Forge 作弊代码的包装，以提高安全性和开发人员体验。
  - [Std Errors](./std-errors.md): 围绕常见的内部 Solidity 错误和 reverts 的包装。
  - [Std Storage](./std-storage.md): 用于存储操作的工具类。
  - [Std Math](./std-math.md): 实用的数学函数。
  - [Script Utils](./script-utils.md): 可以在测试和脚本中访问的工具类。
  - [Console Logging](./console-log.md): 控制台日志功能。

- 一个作弊代码实例 `vm`，你可以通过它调用 Forge 作弊代码（见 [作弊代码参考](.../.../cheatcodes/)）。

    ```solidity
    vm.startPrank(alice);
    ```

- 所有 Hardhat `console` 功能的记录（见 [控制台日志](./console-log.md)）

    ```solidity
    console.log(alice.balance); // or `console2`
    ```

- 所有用于断言和日志的 Dappsys 测试函数（见 [Dappsys 测试参考](../ds-test.md)）。

    ```solidity
    assertEq(dai.balanceOf(alice), 10000e18);
    ```

- 工具类函数也包括在 `Script.sol` 中（见[脚本工具类](./script-utils.md)）。

    ```solidity
    // 对于给定的部署者地址和 nonce，计算合约将被部署到的地址
    address futureContract = computeCreateAddress(alice, 1);
    ```
