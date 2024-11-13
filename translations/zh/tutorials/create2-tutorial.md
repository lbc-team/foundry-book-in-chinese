## 使用 CREATE2 进行确定性部署

### 介绍

作为 [Constantinople 分叉](https://ethereum.org/en/history/#constantinople) 的一部分，`CREATE2` 在 2019 年被纳入 EVM。`CREATE2` 是一个从 [EIP-1014](https://eips.ethereum.org/EIPS/eip-1014) 开始的操作码。
`CREATE2` 允许你基于由部署者控制的参数，将智能合约部署到确定的地址。
因此，它通常被提及为启用“反事实”部署，你可以与尚未创建的地址进行交互，因为 `CREATE2` 保证已知代码可以放置在该地址。
这与 `CREATE` 操作码形成对比，后者的部署合约地址是部署者的 nonce 的函数。
使用 `CREATE2`，你可以使用相同的部署者账户在多个网络上将合约部署到相同的地址，即使该地址的 nonce 不同。

> ℹ️ **注意**
>本指南旨在帮助理解 `CREATE2`。在大多数用例中，你不需要编写和使用自己的部署者，可以使用现有的确定性部署者。在 forge 脚本中，使用 `new MyContract{salt: salt}()` 将使用位于 [0x4e59b44847b379578588920ca78fbf26c0b4956c](https://github.com/Arachnid/deterministic-deployment-proxy) 的确定性部署者。

在本教程中，我们将：

1. 查看 `CREATE2` 工厂实现。
2. 使用传统的部署方法部署工厂。
3. 使用已部署的工厂来反过来在确定性地址部署一个简单的计数器合约。
4. 通过在 Foundry 中编写简单的测试来模拟这一系列事件。

### 先决条件

1. 需要对 Solidity 和 Foundry 有一定了解，并建议对内联汇编有一定了解。请参考 [官方 Solidity 文档](https://docs.soliditylang.org/en/latest/assembly.html)了解内联汇编的基础知识。
2. 确保你的系统上已经 [安装](../getting-started/installation.md) 了 Foundry。
3. [初始化](../projects/creating-a-new-project.md)一个新的 Foundry 项目。

### CREATE2 工厂

在 `src` 目录下创建一个名为 `Create2.sol` 的文件。
像这样初始化一个名为 `Create2` 的合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Create2 {

    error Create2EmptyBytecode();

    error Create2FailedDeployment();
}
```

这些错误旨在对工厂部署执行一些合理性检查，并在触发时回滚整个交易。如果传递给 `deploy` 函数的字节码为空，则会触发 `Create2EmptyBytecode()` 错误，如果部署由于任何原因失败，则会触发 `Create2FailedDeployment()` 错误。

> ℹ️ **注意**
>
> 请注意，由于多种原因，`CREATE2` 部署可能会失败。例如，如果字节码无效，或者如果在计算地址时已经部署了合约。如果你的构造函数由于任何原因回滚，你的部署也可能会失败。

接下来，创建一个名为 `deploy` 的函数：

```solidity
function deploy(bytes32 salt, bytes memory creationCode) external payable returns (address addr) {
 
 }
```

此函数接受 2 个输入：

1. 用于计算最终地址的 `salt`。基本上，这可以是我们想要的任何随机值。
2. 我们想要部署的合约的创建代码。

成功部署后，新部署的合约地址将被返回。

> ℹ️ **注意**
>
> 你可以向使用 `CREATE2` 部署的合约发送 ETH，但前提是它具有可支付的构造函数。如果你尝试在没有可支付构造函数的情况下向其发送 ETH，交易将回滚。

在部署函数的顶部添加此回滚语句。我们希望我们的函数在不满足以下条件时拒绝部署请求：

```solidity
    if (creationCode.length == 0) {
        revert Create2EmptyBytecode();
    }
```

接下来，我们将使用内联汇编调用 `CREATE2` 操作码，可以使用 `assembly` 关键字来完成：

要从内联汇编中调用 [CREATE2 操作码](https://docs.soliditylang.org/en/latest/yul.html#evm-dialect) ，我们需要传入 4 个参数：

```solidity
    assembly {
        addr := create2(callvalue(), add(creationCode, 0x20), mload(creationCode), salt)
    }
```

1. 我们希望作为部署的一部分发送到新地址的 ETH 数量。在这里，我们只传入 `callvalue()`，这是作为交易的一部分发送到工厂合约的 ETH 数量。可以将其视为 `msg.value` 的低级版本。
2. 第二个和第三个参数是我们的字节码所在内存范围。`add(bytecode, 0x20)` 接受一个对 `bytes` 变量 bytecode 在内存中的位置的引用，并跳过 32 字节（十六进制中的 0x20）以指向实际的字节码。

> ℹ️ **注意**
>
> Solidity 中的 `bytes` 类型是一个动态大小的字节数组，其中内存的前 32 个字节表示数组的长度，其余字节表示实际数据。因此，当我们传入对 `bytes` 变量的引用时，我们需要跳过前 32 个字节以指向实际数据。
> 在 [evm.codes](https://www.evm.codes/?fork=shanghai) 上阅读有关 `add` 和 `mload` 操作码的更多信息。

最后，如果由于任何原因部署失败，我们将回滚整个交易，在这种情况下，`CREATE2` 操作码将返回一个 0 地址：

```solidity
        if (addr == address(0)) {
            revert Create2FailedDeployment();
        }
```

最后，让我们创建一个名为 `computeAddress` 的视图函数。此函数应接受 `salt` 和 `creationCode` 作为参数，并返回使用 `deploy` 函数将部署的合约的地址：

```solidity
function computeAddress(bytes32 salt, bytes32 creationCodeHash) external view returns (address addr) {

 }
```

在函数内部，粘贴以下代码，该代码使用内联汇编来通过执行与 `CREATE2` 操作码相同的计算来计算地址：

```solidity
    address contractAddress = address(this);
        
    assembly {
        let ptr := mload(0x40)

        mstore(add(ptr, 0x40), creationCodeHash)
        mstore(add(ptr, 0x20), salt)
        mstore(ptr, contractAddress)
        let start := add(ptr, 0x0b)
        mstore8(start, 0xff)
        addr := keccak256(start, 85)
    }
```

在尝试理解此处的汇编代码之前，让我们看一下 `CREATE2` 操作码用于计算地址的公式：

```bash
keccak256(0xff ++ address ++ salt ++ keccak256(bytecode))[12:]
```

`0xff` 是一个硬编码的前缀，用于防止使用 `CREATE` 和 `CREATE2` 部署的地址之间的哈希冲突。`address` 参数是调用 `CREATE2` 操作码的合约的地址，在我们的情况下是工厂合约。这 4 个参数被连接在一起，并使用 `keccak256` 生成一个 32 字节的哈希。前 12 个字节被截断，剩下的 20 个字节被用作部署合约的地址。

`computeAddress` 函数中的整个汇编代码尝试重新创建相同的公式，尽管没有调用 `CREATE2` 操作码：

1. `mload(0x40)` 将空闲内存指针加载到内存中。这是指向内存数组中下一个空闲内存槽的指针。在 [Solidity 文档](https://docs.soliditylang.org/en/latest/assembly.html#memory-management)中了解更多信息。
2. `mstore(add(ptr, 0x40), bytecodeHash)` 将 `bytecodeHash` 存储在由 `ptr + 0x40` 指向的内存位置，即 `ptr+ 64 字节`。
3. `mstore(add(ptr, 0x20), salt)` 将 `salt` 存储在由 `ptr + 0x20` 指向的内存位置。
4. `mstore(ptr, contractAddress)` 将 `contractAddress` 存储在由 `ptr` 指向的内存位置。

> ℹ️ **注意**
>
> 请记住，传递给 `computeAddress` 函数的所有参数都是 32 字节长，并且作为 32 字节值存储在内存中。然而，Solidity 中的地址长度为 20 字节，并且作为 32 字节值存储在内存中，其中前 12 字节被替换为 0。因此，当我们需要跳过 12 个字节以指向实际地址时。

5. `let start := add(ptr, 0x0b)` 创建一个名为 `start` 的新变量，指向内存位置 `ptr + 0x0b` ，即 `ptr + 11 字节`。
6. 最后，可以使用 `mstore8` 操作码在内存位置存储单个字节。在这里，我们将值 `0xff` 存储在由 `start` 指向的内存位置，它占据内存槽的第 12 个字节。
7. 所有值都打包到其正确的内存位置后，我们现在可以在从 `start` 开始的内存槽上调用 `keccak256`，并将内存槽的长度作为第二个参数传递。这将返回一个 32 字节的哈希，我们可以截断以获得最终地址。
ℹ️ **注意**

你可以在此处查看此工厂实现的完整代码 [此处](https://github.com/Genesis3800/CREATE2Factory/blob/main/src/Create2.sol)。
还可以查看 OpenZeppelin 的 [CREATE2 库的实现](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/793d92a3331538d126033cbacb1ee5b8a7d95adc/contracts/utils/Create2.sol)，该实现已被用作本教程的灵感来源。
最后，Forge 提供了一些 `CREATE2` 地址计算辅助函数。 [查看它们](https://github.com/foundry-rs/forge-std/blob/f73c73d2018eb6a111f35e4dae7b4f27401e9421/src/StdUtils.sol#L122-L134)。

### 测试我们的工厂

在 `test` 目录中创建一个名为 `Create2.t.sol` 的文件。
像这样初始化一个名为 `Create2Test` 的合约：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import {Test} from "forge-std/Test.sol";
import {Counter} from "../src/Counter.sol";
import {Create2} from "../src/Create2.sol";

contract Create2Test is Test {

}
```

初始化以下状态变量和 `setUp()` 函数：

```solidity
    Create2 internal create2;
    Counter internal counter;

    function setUp() public {
        create2 = new Create2();
        counter = new Counter();
    }
```

创建一个名为 `testDeterministicDeploy()` 的新函数，该函数：

1. 部署 `Create2` 合约的新实例。
2. 向一个特定地址分配 100 ETH，我们将使用该地址来冒充所有后续调用的调用者，使用 `prank` 作弊码。
3. 设置 `salt` 和 `bytecode` 参数
4. 使用先前部署的 `Create2` 合约在确定性地址上部署 `Counter` 合约。
5. 检查合约是否部署在正确的地址上，通过断言计算的地址是否等于部署的地址。

```solidity
    function testDeterministicDeploy() public {
        vm.deal(address(0x1), 100 ether);

        vm.startPrank(address(0x1));  
        bytes32 salt = "12345";
        bytes memory creationCode = abi.encodePacked(type(Counter).creationCode);

        address computedAddress = create2.computeAddress(salt, keccak256(creationCode));
        address deployedAddress = create2.deploy(salt , creationCode);
        vm.stopPrank();

        assertEq(computedAddress, deployedAddress);  
    }
```

保存所有文件，并使用 `forge test --match-path test/Create2.t.sol -vvvv` 运行测试。
你的测试应该通过，没有任何错误。

> ℹ️ **注意**

本指南旨在帮助理解 `CREATE2`。在大多数情况下，你不需要编写和使用自己的部署器，可以使用现有的确定性部署器。在 forge 脚本中，使用 `new MyContract{salt: salt}()` 将使用地址为 [0x4e59b44847b379578588920ca78fbf26c0b4956c](https://github.com/Arachnid/deterministic-deployment-proxy) 的确定性部署器。
