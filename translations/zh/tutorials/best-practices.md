# 最佳实践

本指南记录了使用 Foundry 进行开发时建议的最佳实践。
一般而言，建议尽可能使用 [`forge fmt`](../reference/config/formatter.md) 处理，不处理的内容如下。

- [一般合约指南](#general-contract-guidance)
- [测试](#tests)
   - [一般测试指南](#general-test-guidance)
   - [分叉测试](#fork-tests)
   - [测试工具](#test-harnesses)
     - [内部函数](#internal-functions)
     - [私有函数](#private-functions)
     - [Workaround函数](#workaround-functions)
   - [最佳实践](#best-practices)
   - [污点分析](#taint-analysis)
- [脚本](#scripts)
- [私钥管理](#private-key-management)
- [评论](#comments)
- [资源](#resources)

## 一般合约指南

1. 始终使用命名导入语法，不要导入完整文件。 这将导入的内容限制为仅指定的项目，而不是文件中的所有内容。 导入完整文件可能会导致 solc 重复定义和 Slither 报错，尤其是当 repos 增长并且具有更多具有重叠名称的依赖项时。

    - Good：`import {MyContract} from "src/MyContract.sol"` 只导入 `MyContract`。
    - Bad：`import "src/MyContract.sol"` 导入 `MyContract.sol` 中的所有内容。 （导入 `forge-std/Test` 或 `Script` 在这里可能是个例外，这样你可以获得控制台库等）。

2. 注意导入的绝对路径和相对路径之间的权衡（绝对路径是相对于 repo 根目录的，例如 `"src/interfaces/IERC20.sol"`），并为你的项目选择最佳方法：

    - 绝对路径可以更轻松地查看文件的来源并减少移动文件时的变动。
    - 相对路径使你的编辑器更有可能提供 linting 和自动完成等功能，因为编辑器/扩展可能无法理解你的重新映射。

3. 如果从依赖项中复制库（而不是导入它），请在配置文件中使用 `ignore = []` 选项以避免格式化该文件。 这使得审阅者和审计员可以更轻松地将其与原始版本进行比较。

4. 同样，随意使用 `// forgefmt: disable-*` 注释指令来忽略手动格式化后看起来更好的代码行/段。 `*` 支持的值是：

    - `disable-line`
    - `disable-next-line`
    - `disable-next-item`
    - `disable-start`
    - `disable-end`

其他最佳实践来自 [samsczun](https://twitter.com/samczsun) 的 [How Do You Even Write Secure Code Anyways](https://www.youtube.com/watch?v=Wm3t8Fuiy1E) ：

- 使用描述性变量名称。
- 限制活动变量的数量。
- 没有多余的逻辑。
- 尽可能提前退出，减少看到代码时的心理负担。
- 相关代码应彼此靠近放置。
- 删除未使用的代码。

## 测试

### 一般测试指导

1. 为了测试 `MyContract.sol`，测试文件应该是`MyContract.t.sol`。 为了测试 `MyScript.s.sol`，测试文件应该是 `MyScript.t.sol`。

    - 如果合约很大并且你想将其拆分为多个文件，请将它们按逻辑分组，如 `MyContract.owner.t.sol`、`MyContract.deposits.t.sol` 等。

2. 永远不要在 `setUp` 函数中做出断言，如果你需要确保 `setUp` 函数执行预期的操作，请使用像 `test_SetUpState()` 这样的专用测试。 有关原因的更多信息，请参见 [foundry-rs/foundry#1291](https://github.com/foundry-rs/foundry/issues/1291)

3. 对于单元测试，组织测试的方式主要有两种：
    1. 将合约视为描述块：
       - `contract Add` 包含在 MyContract 合约中 `add` 方法的所有单元测试。
       - `contract Supply` 包含 `supply` 方法的所有测试。
       - `contract Constructor` 保存构造函数的所有测试。
       - 这种方法的一个好处是较小的合约应该比较大的合约编译得更快，因此随着测试套件变大，许多小型合约的这种方法应该可以节省时间。
    2. 每个被测合约都有一个测试合约，其中包含你想要的任意数量的 Utilities 和 Fixtures：
       - `contract MyContractTest` 包含对 `MyContract` 合约进行的所有的单元测试.
       - `contract VaultTest` 对 `contract Vault` 进行测试，但它也继承自 `contract BaseTestFixture` 和 `contract TestUtilities`。
       - 测试函数的编写顺序应与被测合约中存在的原始函数相同。
       - 测试同一功能的所有测试功能都应连续存在于测试文件中。
4. 集成测试应该放在同一个 `test` 目录中，并有明确的命名约定。 这些可能位于专用文件中，或者它们可能与现有测试文件中的相关单元测试相邻。

5. 保持测试命名一致，这对于筛选测试非常有帮助（例如，对于 Gas 报告，你可能希望筛选掉 revert 测试）。在结合命名约定时，保持它们按字母顺序排列。下面是一些有效名称的示例。可以在[这里](https://github.com/ScopeLift/scopelint/blob/1857e3940bfe92ac5a136827374f4b27ff083971/src/check/validators/test_names.rs#L106-L143)找到有效和无效示例的全面列表。

    - `test_Description` 用于标准测试的。
    - `testFuzz_Description` 用于模糊测试。
    - `test_Revert[If|When]_Condition` 用于期望 revert 的测试。
    - `testFork_Description` 用于从网络分叉的测试。
    - `testForkFuzz_Revert[If|When]_Condition` 用于分叉并期望 revert 的模糊测试。

6. 使用`ALL_CAPS_WITH_UNDERSCORES`命名你的常量和不可变量，以便更容易区分它们与变量和函数。

7. 当使用像 `assertEq` 这样的断言时，考虑利用最后一个字符串参数来更容易地识别失败。 这些可以保持简短，甚至只是数字——它们基本上可以替代显示 revert 的行号，例如 `assertEq(x, y, "1")` 或 `assertEq(x, y, "sum1")`。 _（注意：[foundry-rs/foundry#2328](https://github.com/foundry-rs/foundry/issues/2328) 跟踪本地集成）。_

8. 测试事件时，最好将所有 `expectEmit` 参数设置为 `true`，即 `vm.expectEmit(true, true, true, true)`。 好处：

    - 这可确保你测试事件中的所有内容。
    - 如果你添加一个 topic（即一个新的索引参数），它现在默认进行测试。
    - 即使你只有 1 个 topic，额外的 `true` 参数也没有坏处。

9. 记得写不变量测试！ 对于断言字符串，使用不变量的冗长英文描述：`assertEq(x + y, z, "Invariant violated: the sum of x and y must always equal z")`。 有关最佳实践的更多信息即将推出。


### 分叉测试

1. 不要觉得你需要给分叉测试特殊待遇，并自由使用它们：

    - 闭源 web2 开发中_需要_模拟——你必须模拟 API 响应，因为该 API 的代码不是开源的，所以你不能直接在本地运行它。 但对于区块链而言，情况并非如此：你正在与之交互的任何已部署代码都可以在本地执行，甚至可以免费修改。 所以，如果不需要，为什么要引入错误模拟的风险呢？
    - 避免分叉测试而更喜欢模拟的一个常见原因是分叉测试很慢。 但这并不总是正确的。 通过固定到一个块号，forge 缓存 RPC 响应，因此只有第一次运行速度较慢，而后续运行速度明显更快。 请参阅[this benchmark](https://github.com/mds1/convex-shutdown-simulation/)，第一次使用远程 RPC 运行需要 7 分钟，但一旦数据被缓存结果只需要半秒。 [Alchemy](https://alchemy.com) 、 [Infura](https://infura.io) 和 [Tenderly](https://tenderly.co) 都提供免费存档数据，因此固定到一个块应该没有问题。 （请注意，你可能需要配置 CI 以缓存运行之间的 RPC 响应）。
    - 请注意，[foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain) GitHub Action 默认情况下会在 CI 中缓存 RPC 响应，并且在更新 fork 测试时也会更新缓存。

2. 在 fork 上小心使用模糊测试，以避免通过非确定性模糊测试消耗 RPC 请求。如果你的 fork 模糊测试的输入是用于获取数据的 RPC 调用中的某个参数（例如查询地址的代币余额），每次运行模糊测试都会使用至少 1 个 RPC 请求，因此你很快就会达到速率限制或使用限制。需要考虑的解决方案：

   - 用单个 [multicall](https://github.com/mds1/multicall) 替换多个 RPC 调用。
   - 指定一个模糊/不变的 [seed](/src/reference/config/testing.md#seed)：这可以确保每次 `forge test` 调用使用相同的模糊输入。RPC 结果会在本地缓存，因此你只会在第一次查询节点。
   - 在 CI 中，考虑使用 [计算环境变量](https://github.com/sablier-labs/v2-core/blob/d1157b49ed4bceeff0c4e437c9f723e88c134d3a/.github/workflows/ci.yml#L252-L254) 设置模糊种子，使其每天或每周更改一次。这样可以灵活地权衡增加随机性以发现更多错误与使用种子以减少 RPC 请求之间的权衡。
   - 构造你的测试，使你进行模糊测试的数据由你的合约在本地计算，而不是在 RPC 调用中使用的数据（根据你的操作可能可行或不可行）。
   - 最后，你当然可以始终运行本地节点或升级你的 RPC 计划。

3. 编写分叉测试时，不要使用 `--fork-url` 标志。 相反，更喜欢以下方法，因为它提高了灵活性：

    - 在 `foundry.toml` 配置文件中定义 `[rpc_endpoints]` 并使用 [forking cheatcodes](../forge/fork-testing.md#forking-cheatcodes)。
    - 使用 forge-std 的 `stdChains.ChainName.rpcUrl` 访问测试中的 RPC URL 端点。 请参阅 [此处](https://github.com/foundry-rs/forge-std/blob/ff4bf7db008d096ea5a657f2c20516182252a3ed/src/StdCheats.sol#L255-L271) 的支持链列表和预期的配置文件别名。
    - 始终固定到一个块，以便测试具有确定性并缓存 RPC 响应。
    - 有关此分叉测试方法的更多信息，请参见 [此处](https://twitter.com/msolomon44/status/1564742781129502722)（这早于 `StdChains`，因此该方面有点过时了）。



### 测试工具

#### 内部函数

要测试 `internal` 函数，请编写一个继承自被测合约 (CuT) 的工具合约。 从 CuT 继承的工具合约将 `internal` 函数暴露为 `external` 函数。

每个被测试的 `internal` 函数都应该通过一个名称遵循 `exposed_<function_name>` 名称的外部函数暴露出来。 例如：

```solidity
// file: src/MyContract.sol
contract MyContract {
  function myInternalMethod() internal returns (uint) {
    return 42;
  }
}

// file: test/MyContract.t.sol
import {MyContract} from "src/MyContract.sol";

contract MyContractHarness is MyContract {
  // Deploy this contract then cll this method to test `myInternalMethod`.
  function exposed_myInternalMethod() external returns (uint) {
    return myInternalMethod();
  }
}
```

#### 私有函数

不幸的是，目前没有好的方法来对 `private` 方法进行单元测试，因为它们不能被任何其他合约访问。 可选如下：

- 将 `private` 函数转换为 `internal` 。
- 将逻辑复制/粘贴到你的测试合约中，并编写一个在 CI 检查中运行的脚本，以确保两个函数相同。

#### Workaround 函数

Harnesses 还可用于公开原始智能合约中不可用的功能或信息。 最直接的例子是当我们想要测试公共数组的长度时。 这些函数应遵循以下模式：`workaround_<function_name>`，例如 `workaround_queueLength()`。

另一个用例是跟踪你不会在生产中跟踪的数据，以帮助测试不变量。 例如，你可以存储所有代币持有者的列表，以简化不变式“所有余额的总和必须等于总供应量”的验证。 这些通常被称为“幽灵变量”。 你可以在 [Rikard Hjort](https://twitter.com/rikardhjort) 的 [工作 DeFi 开发的正式方法](https://youtu.be/ETlNhV9jYJw) 演讲中了解更多相关信息。

### 最佳实践

感谢 [@samsczun](https://twitter.com/samczsun) 的 [How Do You Even Write Secure Code Anyways](https://www.youtube.com/watch?v=Wm3t8Fuiy1E) 演讲中提供的本节和下节的技巧。

- 不要针对覆盖率进行优化，而是针对经过深思熟虑的测试进行优化。
- 编写正面和负面的单元测试。
   - 为代码应该处理的事情编写正面单元测试。 验证所有从这些测试中更改的状态。
   - 为代码不应该处理的事情编写负面单元测试。 跟进正面测试（作为相邻测试），并对其进行修改，以使其通过，这很有用的。
   - 每个代码路径都应该有自己的单元测试。
- 编写集成测试来测试整个功能。
- 编写分叉测试以验证现有已部署合约的正确行为。

### 污点分析

测试时，你应该优先考虑攻击者可以影响的函数，即接受某种用户输入的函数。 这些被称为 _污点源（sources）_。

将输入数据视为 _污点（tainted）_ ，直到它被代码检查过，此时它被认为是干净的。

_污点汇聚点（sink）_ 是发生某些重要操作的代码的一部分。 例如，在 MakerDAO 中，它将是`vat.sol`。

你应该 _确保_ 任何 _污点（tainted）_ 都不会到达 _污点汇聚点（sink）_。 这意味着在污点汇聚点（sink）能够找到的数据的数据，应该在某个时候，已经被你检查过了。 因此，你需要定义数据 _应该_ 是什么，然后确保你的检查能 _确保_ 数据符合你的预期。

## 脚本

1. 为清晰起见，坚持使用 `run` 作为默认函数名称。

2. 任何不打算在脚本中直接调用的方法都应该是 `internal` 或 `private`。 一般来说，唯一的公共方法应该是 `run`，因为当每个脚本文件只做一件事时，它更容易阅读/理解。

3. 考虑根据脚本在协议生命周期中的运行顺序为脚本添加前缀。 例如，`01_Deploy.s.sol`、`02_TransferOwnership.s.sol`。 这使事情更加自我描述。 这可能并不总是适用，具体取决于你的项目。

4. 测试你的脚本。

    - 通过编写测试来对它们进行单元测试，这些测试断言运行脚本所做的状态更改。
    - 通过运行该脚本编写你的部署脚本和脚手架测试。 然后，针对生产部署脚本产生的状态运行所有测试。 这是获得对部署脚本的信心的好方法。
    - 在你的脚本中使用 `require` 语句（或者如果你喜欢的话，使用 `if (condition) revert()` 模式）来在脚本出现问题时停止执行。例如，`require(computedAddress == deployedAddress, "address mismatch")`。使用 `assertEq` 辅助函数将不会停止执行。

5. **仔细审核广播了哪些交易**。 未广播的事务仍在测试上下文中执行，因此缺少广播或额外广播很容易成为上一步中的错误来源。

6. **注意抢跑**。 Forge 模拟你的脚本，根据模拟结果生成交易数据，然后广播交易。 确保你的脚本对模拟和广播之间的链状态变化具有很好的防护。 下面是一个容易受到此影响的示例脚本：

    ```solidity
        // 伪代码，可能无法编译。
        contract VulnerableScript is Script {
        function run() public {
            vm.startBroadcast();
        
            // 交易 1：使用 CREATE 部署一个新的 Gnosis Safe。
            // 因为我们使用的是 CREATE 而不是 CREATE2，新 Safe 的地址是
            // gnosisSafeProxyFactory 的 nonce 的函数。
            address mySafe = gnosisSafeProxyFactory.createProxy(singleton, data);
        
            // 交易 2：向新的 Safe 发送代币。
            // 我们知道 mySafe 的地址是 gnosisSafeProxyFactory 的 nonce 的函数。
            // 如果在模拟和广播之间有人部署了一个 Gnosis Safe，mySafe 的地址将会不同，
            // 这个脚本将会把 1000 DAI 发送到其他人的 Safe。在这种情况下，我们可以通过
            // 使用 CREATE2 而不是 CREATE 来保护自己，但每种情况可能有不同的解决方案。
            dai.transfer(mySafe, 1000e18);
        
            vm.stopBroadcast();
        }
        }
    ```

1. 对于从 JSON 输入文件读取的脚本，将输入文件放在 `script/input/<chainID>/<description>.json` 中。 然后将 `run(string memory input)`（如果需要读取多个文件，则采用多个字符串输入）作为脚本的签名，并使用以下方法读取 JSON 文件。

    ```solidity
    function readInput(string memory input) internal returns (string memory) {
    string memory inputDir = string.concat(vm.projectRoot(), "/script/input/");
    string memory chainDir = string.concat(vm.toString(block.chainid), "/");
    string memory file = string.concat(input, ".json");
    return vm.readFile(string.concat(inputDir, chainDir, file));
    }
    ```

### 私钥管理

脚本执行需要私钥来发送交易。该私钥控制账户中的所有资金，因此必须小心保护。通过脚本安全广播交易有几种选择：

1. **使用硬件钱包。** Ledger 和 Trezor 等硬件钱包将种子短语存储在安全隔离区中。Forge 可以向钱包发送原始交易，然后钱包将对交易进行签名。签名后的交易返回给 Forge 和广播者。这样，私钥永远不会离开硬件钱包，使其成为非常安全的方法。要在脚本中使用硬件钱包，请参阅 `--ledger` 和 `--trezor` [flags](../reference/forge/forge-script.md)。

2. **直接使用私钥。** 通过这种方法，在你的设备上暴露了一个私钥，使其比上述选项更有风险。因此，直接使用私钥的建议方式是为执行脚本生成一个新钱包，并仅向该钱包发送足够的资金来运行脚本。然后，在脚本完成后停止使用该密钥。这样，如果密钥被泄露，只会丢失该一次性密钥上的资金，而不是失去钱包中的所有资金。

   1. 通过这种方法，非常重要的是，你的脚本或合约不依赖于 `msg.sender`，因为发送方将不是一个打算再次使用的账户。例如，如果部署脚本配置了合同所有者，请确保所有者是构造函数参数，而不是设置为 `msg.sender`。
   2. 要使用这种方法，你可以将私钥存储在环境变量中并使用秘籍代码来读取它，或者使用 `--private-key` 标志直接提供密钥。

3. **使用密钥库。** 这可以被视为上述两种方法之间的折衷方案。使用 [`cast wallet import`](../reference/cast/cast-wallet-import.md) 导入私钥并使用密码对其进行加密。这仍会在你的设备上暴露你的私钥，但它会被加密，你将提供密码来解密它以运行脚本。

在使用脚本时的额外安全预防措施：

1. 在测试和开发中使用单独的钱包，而不是使用带有真实资金的主要钱包。多样化可以最大程度地减少如果你的开发钱包受到损害而丢失资金的风险。
2. 如果你意外地将私钥或种子短语推送到 GitHub，或者通过其他方式在线暴露它，即使是短暂的，也要视为已泄露。立即采取行动将你的资金转移到更安全的地方。
3. 如果对钱包是否包含真实资金存在疑问，请假设它包含。在开发过程中始终确保钱包的余额和状态。使用 [blockscan](https://blockscan.com/) 轻松检查许多链以查看地址的使用情况。
4. 请记住，在像 Metamask 这样的钱包中添加账户会生成一个新的私钥。但是，该私钥是从相同助记词派生的，与该钱包中生成的其他账户相同。因此，永远不要暴露助记词，因为这可能会危及你所有的账户。

*本节内容灵感来自 [The Pledge](https://github.com/smartcontractkit/full-blockchain-solidity-course-js/discussions/5) 由 [Patrick Collins](https://twitter.com/PatrickAlphaC) 提供。* 

## 注释

1. 对于公共或外部方法和变量，使用 [NatSpec](https://docs.soliditylang.org/en/latest/natspec-format.html) 注释。

    - `forge doc` 将解析这些以自动生成文档。
    - Etherscan 将在合约 UI 中显示它们。

1. 对于简单的 NatSpec 注释，考虑只在文档字符串中记录参数，例如 `` /// @notice 返回 `x` 和 `y` 的总和。 ``，而不是使用 `@param` 标签。

1. 对于复杂的 NatSpec 注释，考虑使用像 [PlantUML](https://plantuml.com/ascii-art) 这样的工具来生成 ASCII 艺术图，以帮助解释代码库的复杂方面。

1. 当使用 `forge doc` 生成文档时，你注释中的任何 markdown 都将正确保留，因此在有用时使用 markdown 结构注释。

    - 好：`/// @notice Returns the sum of `x` and `y`.`
    - 错误：`/// @notice Returns the sum of x and y.`

## 资源

编写更安全的代码和更好的测试：

- [transmissions11/solcurity](https://github.com/transmissions11/solcurity)
- [nascentxyz/simple-security-toolkit](https://github.com/nascentxyz/simple-security-toolkit)

Foundry 在行动：

- [awesome-foundry](https://github.com/crisgarner/awesome-foundry): 一个由 Foundry 开发框架精心策划的精彩清单。
- [Nomad Monorepo](https://github.com/nomad-xyz/monorepo): 所有 `contracts-*` 包。是使用许多 Foundry 功能的良好示例，包括模糊测试、`ffi` 和各种作弊代码。
- [Sablier V2 Core](https://github.com/sablier-labs/v2-core): 另一个使用了许多 Foundry 功能的良好示例。也是状态树测试方法的先驱，查看 `*.tree` 文件。
- [Uniswap Periphery](https://github.com/gakonst/v3-periphery-foundry): 使用继承来隔离测试装置的良好示例。
- [PRBMath](https://github.com/PaulRBerg/prb-math): 用于 Solidity 的固定点算术库，具有许多利用 Foundry 的参数化测试。
