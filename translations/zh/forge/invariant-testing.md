# 不变量测试
## 概述

不变量测试允许对预定义合约中的预定义函数调用序列进行随机化测试，以验证一组不变量表达式。在每次函数调用执行后，都会断言所有定义的不变量。

不变量测试是揭示协议中错误逻辑的强大工具。由于函数调用序列是随机化的，并且输入是模糊的，不变量测试可以在边缘情况和高度复杂的协议状态中揭示错误的假设和不正确的逻辑。

不变量测试活动有两个维度，`runs` 和 `depth`：
- `runs`：生成并运行函数调用序列的次数。
- `depth`：在给定的 `run` 中进行的函数调用次数。在每次函数调用后都会断言不变量。如果函数调用回退，`depth` 计数器仍然递增。

> ℹ️ **注意**
>
> 在实现不变量测试时，重要的是要注意对于每个 `invariant_*` 函数，会创建一个不同的 EVM 执行器，因此不变量不会针对相同的 EVM 状态进行断言。这意味着如果定义了 `invariant_A()` 和 `invariant_B()` 函数，那么 `invariant_B()` 不会针对 `invariant_A()` 的 EVM 状态进行断言（反之亦然）。
>
> 如果你想同时断言所有不变量，可以将它们分组并在多个作业上运行。例如，使用两个作业断言所有不变量可以实现为：
> ```Solidity
>function invariant_job1() public {
>    assertInvariants();
>}
>
>function invariant_job2() public {
>    assertInvariants();
>}
>
>function assertInvariants() internal {
>    assertEq(val1, val2);
>    assertEq(val3, val4);
>}
> ```

这些和其他不变量配置方面的内容在 [`这里`](#configuring-invariant-test-execution) 进行了说明。

类似于在 Foundry 中通过在函数名称前加上 `test` 来运行标准测试，不变量测试通过在函数名称前加上 `invariant` 来表示（例如，`function invariant_A()`）。

`afterInvariant()` 函数在每次不变量运行结束时调用（如果声明了），允许进行后期活动处理。此函数可用于记录活动指标（例如选择器被调用的次数）和后期模糊活动测试（例如关闭所有头寸并断言所有资金能够退出系统）。

### 配置不变量测试执行

不变量测试的执行由用户通过 Forge 配置原语控制的参数管理。配置可以全局应用或按测试应用。有关此主题的详细信息，请参阅 📚 [`全局配置`](../reference/config/testing.md) 和 📚 [`内联配置`](../reference/config/inline-test-config.md)。

## 定义不变量

不变量是在模糊测试活动过程中应始终保持为真的条件表达式。一个好的不变量测试套件应尽可能多地包含不变量，并且可以为不同的协议状态提供不同的测试套件。

不变量的示例包括：
- *"xy=k 公式始终成立"* 对于 Uniswap
- *"所有用户余额的总和等于总供应量"* 对于 ERC-20 代币。

有不同的方法来断言不变量，如下表所示：

<table>
<tr><th>类型</th><th>解释</th><th>示例</th></tr>

<tr>

<td>直接断言</td>
<td>查询协议智能合约并断言值符合预期。</td>
<td>

```solidity
assertGe(
    token.totalAssets(),
    token.totalSupply()
)
```
</td>

</tr>

<tr>

<td>幽灵变量断言</td>
<td>查询协议智能合约并将其与测试环境中持久化的值（幽灵变量）进行比较。</td>
<td>

```solidity
assertEq(
    token.totalSupply(),
    sumBalanceOf
)
```
</td>

</tr>

<tr>

<td>去优化（天真实现断言）</td>
<td>查询协议智能合约并将其与同一逻辑的天真且通常是高 gas 消耗的实现进行比较。</td>
<td>

```solidity
assertEq(
    pool.outstandingInterest(),
    test.naiveInterest()
)
```
</td>

</tr>
</table>

### 条件不变量

不变量必须在给定的模糊测试活动过程中保持，但这并不意味着它们必须在每种情况下都成立。在某些场景中，可能会引入/移除某些不变量（例如，在清算期间）。

不建议在不变量断言中引入条件逻辑，因为它们可能会因为不正确的代码路径而引入误报。例如：

```solidity
function invariant_example() external {
    if (protocolCondition) return;

    assertEq(val1, val2);
}
```

在这种情况下，如果 `protocolCondition == true`，则不变量根本不会被断言。有时这可能是期望的行为，但如果 `protocolCondition` 在整个模糊测试活动中意外为真，或者条件本身存在逻辑错误，则可能会导致问题。因此，最好为该条件定义一个替代不变量，例如：

```solidity
function invariant_example() external {
    if (protocolCondition) {
        assertLe(val1, val2);
        return;
    };

    assertEq(val1, val2);
}
```

处理不同协议状态下的不同不变量的另一种方法是为不同场景使用专用的不变量测试合约。这些场景可以使用 `setUp` 函数进行引导，但利用*不变量目标*来引导模糊器以仅产生某些结果的方式行为更为强大（例如，避免清算）。

## 不变量目标

**目标合约**：在给定的不变量测试模糊活动过程中将被调用的合约集。此合约集默认为在 `setUp` 函数中部署的所有合约，但可以自定义以允许更高级的不变量测试。

**目标发送者**：不变量测试模糊器在执行模糊活动时随机选择 `msg.sender` 的值，以默认模拟系统中的多个参与者。如果需要，可以在 `setUp` 函数中自定义发送者集。

**目标接口**：在 `setUp` 期间未部署但在分叉环境中模糊的地址及其项目标识符集（例如 `[(0x1, ["IERC20"]), (0x2, ("IOwnable"))]`）。这使得可以针对委托代理和使用 `create` 或 `create2` 部署的合约进行目标设置。

**目标选择器**：模糊器用于不变量测试的函数选择器集。这些可以用于在给定目标合约中使用函数的子集。

**目标工件**：用于给定合约的所需 ABI。这些可以用于代理合约配置。

**目标工件选择器**：用于给定合约的所需 ABI 中的函数选择器子集。这些可以用于代理合约配置。

在目标冲突情况下，不变量模糊器的优先级为：

`targetInterfaces | targetSelectors > excludeSelectors | targetArtifactSelectors > excludeContracts | excludeArtifacts > targetContracts | targetArtifacts`

### 函数调用概率分布

这些合约中的函数将以随机方式（具有均匀分布的概率）被调用，并带有模糊输入。

例如：

```text
targetContract1:
├─ function1: 20%
└─ function2: 20%

targetContract2:
├─ function1: 20%
├─ function2: 20%
└─ function3: 20%
```

这在设计目标合约时需要注意，因为由于这种概率分布，功能较少的目标合约每个功能将更频繁地被调用。

### 不变量测试辅助函数

不变量测试辅助函数包含在 [`forge-std`](https://github.com/foundry-rs/forge-std/blob/master/src/StdInvariant.sol) 中，以允许配置不变量测试设置。辅助函数概述如下：

| 函数 | 描述 |
|-|-|
| `excludeContract(address newExcludedContract_)` | 将给定的地址添加到`_excludedContracts`数组中。此合同集被明确排除在目标合同之外。|
| `excludeSelector(FuzzSelector memory newExcludedSelector_)` | 将给定的`FuzzSelector`添加到`_excludedSelectors`数组中。此选择器集被明确排除在目标合约选择器之外。 |
| `excludeSender(address newExcludedSender_)` | 将给定的地址添加到`_excludedSenders`数组中。此地址集被明确排除在目标发送者之外。 |
| `excludeArtifact(string memory newExcludedArtifact_)` | 将给定的字符串添加到`_excludedArtifacts`数组中。此字符串集被明确排除在目标工件之外。 |
| `targetArtifact(string memory newTargetedArtifact_)` | 将给定的字符串添加到`_targetedArtifacts`数组中。此字符串集用于目标工件。 |
| `targetArtifactSelector(FuzzArtifactSelector memory newTargetedArtifactSelector_)` | 将给定的`FuzzArtifactSelector`添加到`_targetedArtifactSelectors`数组中。此选择器集用于目标工件选择器。 |
| `targetContract(address newTargetedContract_)` | 将给定的地址添加到`_targetedContracts`数组中。此地址集用于目标合同。此数组覆盖在`setUp`期间部署的合同集。 |
| `targetSelector(FuzzSelector memory newTargetedSelector_)` | 将给定的`FuzzSelector`添加到`_targetedSelectors`数组中。此选择器集用于目标合约选择器。 |
| `targetSender(address newTargetedSender_)` | 将给定的地址添加到`_targetedSenders`数组中。此地址集用于目标发送者。 |
| `targetInterface(FuzzInterface memory newTargetedInterface_)` | 将给定的`FuzzInterface`添加到`_targetedInterfaces`数组中。此接口集扩展了要模糊化的合约和选择器，并使目标地址在`setUp`期间没有部署的环境下使用，例如在分叉环境中进行模糊测试时。此外，还可以针对使用`create`或`create2`部署的代理和合约。 |

### 目标合约设置

目标合约可以通过以下三种方法进行设置：
1. 手动添加到`targetContracts`数组中的合约将被添加到目标合约集。
2. 在`setUp`函数中部署的合约会自动添加到目标合约集中（仅在没有通过选项 1 手动添加任何合约时有效）。
3. 如果在`setUp`中部署的合约被添加到`excludeContracts`数组中，则可以从目标合约中**移除**这些合约。

## 开放测试

针对目标合约的默认配置设置为在设置期间部署的所有合约。对于较小的模块和更多算术合约，这种方式效果不错。例如：

```solidity
contract ExampleContract1 {

    uint256 public val1;
    uint256 public val2;
    uint256 public val3;

    function addToA(uint256 amount) external {
        val1 += amount;
        val3 += amount;
    }

    function addToB(uint256 amount) external {
        val2 += amount;
        val3 += amount;
    }

}
```

此合约可以使用默认的目标合约模式进行部署和测试：

```solidity
contract InvariantExample1 is Test {

    ExampleContract1 foo;

    function setUp() external {
        foo = new ExampleContract1();
    }

    function invariant_A() external {
        assertEq(foo.val1() + foo.val2(), foo.val3());
    }

    function invariant_B() external {
        assertGe(foo.val1() + foo.val2(), foo.val3());
    }

}
```

此设置将以 50%-50%的概率分布调用`foo.addToA()`和`foo.addToB()`，输入经过模糊测试。在测试过程中，输入将不可避免地导致溢出，并且函数调用将开始恢复。由于不变量测试的默认配置是`fail_on_revert = false`，这不会导致测试失败。整个模糊测试过程中不变量将维持，并且测试将成功通过。输出如下：

```text
[PASS] invariant_A() (运行: 50, 调用: 10000, 恢复: 5533)
[PASS] invariant_B() (运行: 50, 调用: 10000, 恢复: 5533)
```

## 基于处理程序的测试

对于更复杂和集成的协议，需要更复杂的目标合约使用方式才能实现期望的结果。为了说明如何利用处理程序，将使用以下合约（接受另一个 ERC-20 代币存款的基于 ERC-4626 的合约）：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.17;

interface IERC20Like {

    function balanceOf(address owner_) external view returns (uint256 balance_);

    function transferFrom(
        address owner_,
        address recipient_,
        uint256 amount_
    ) external returns (bool success_);

}

contract Basic4626Deposit {

    /**********************************************************************************************/
    /*** Storage                                                                                ***/
    /**********************************************************************************************/

    address public immutable asset;

    string public name;
    string public symbol;

    uint8 public immutable decimals;

    uint256 public totalSupply;

    mapping(address => uint256) public balanceOf;

    /**********************************************************************************************/
    /*** Constructor                                                                            ***/
    /**********************************************************************************************/

    constructor(address asset_, string memory name_, string memory symbol_, uint8 decimals_) {
        asset    = asset_;
        name     = name_;
        symbol   = symbol_;
        decimals = decimals_;
    }

    /**********************************************************************************************/
    /*** External Functions                                                                     ***/
    /**********************************************************************************************/

    function deposit(uint256 assets_, address receiver_) external returns (uint256 shares_) {
        shares_ = convertToShares(assets_);

        require(receiver_ != address(0), "ZERO_RECEIVER");
        require(shares_   != uint256(0), "ZERO_SHARES");
        require(assets_   != uint256(0), "ZERO_ASSETS");

        totalSupply += shares_;

        // Cannot overflow because totalSupply would first overflow in the statement above.
        unchecked { balanceOf[receiver_] += shares_; }

        require(
            IERC20Like(asset).transferFrom(msg.sender, address(this), assets_),
            "TRANSFER_FROM"
        );
    }

    function transfer(address recipient_, uint256 amount_) external returns (bool success_) {
        balanceOf[msg.sender] -= amount_;

        // Cannot overflow because minting prevents overflow of totalSupply,
        // and sum of user balances == totalSupply.
        unchecked { balanceOf[recipient_] += amount_; }

        return true;
    }

    /**********************************************************************************************/
    /*** Public View Functions                                                                  ***/
    /**********************************************************************************************/

    function convertToShares(uint256 assets_) public view returns (uint256 shares_) {
        uint256 supply_ = totalSupply;  // Cache to stack.

        shares_ = supply_ == 0 ? assets_ : (assets_ * supply_) / totalAssets();
    }

    function totalAssets() public view returns (uint256 assets_) {
        assets_ = IERC20Like(asset).balanceOf(address(this));
    }

}

```

### 处理程序函数

此合约的 `deposit` 函数要求调用者具有 ERC-20 `asset` 的非零余额。在开放不变量测试方法中，将使用 50-50%的分布调用 `deposit()` 和 `transfer()`，但它们将在每次调用时回滚。这将导致不变量测试“通过”，但实际上在所需的合约中根本没有操作状态。这就是可以利用目标合约的地方。当合约需要一些额外逻辑以使其正常运行时，可以将其添加到名为 `处理程序` 的专用合约中。

```solidity
function deposit(uint256 assets) public virtual {
    asset.mint(address(this), assets);

    asset.approve(address(token), assets);

    uint256 shares = token.deposit(assets, address(this));
}
```

此合约将在进行函数调用之前提供必要的设置，以确保其成功。

基于此概念，处理程序可用于开发更复杂的不变量测试。在开放不变量测试中，测试将如下图所示运行，直接对协议合约进行具有模糊参数的随机序列的函数调用。这将导致更复杂系统的回滚，如上文所述。

![空白图表](https://user-images.githubusercontent.com/44272939/214752968-5f0e7763-d52e-43e6-b453-cac935f5d97d.svg)

通过手动将所有处理程序合约添加到 `targetContracts` 数组中，可以以由处理程序管理的方式进行对协议合约的所有函数调用。这在下图中有详细说明。

![不变量图表 - 第 2 页](https://user-images.githubusercontent.com/44272939/216420091-8a5c2bcc-d586-458f-be1e-a9ea0ef5961f.svg)

通过在模糊器和协议之间增加此层，可以实现更强大的测试。

### 处理程序幽灵变量

在处理程序中，可以跟踪 “幽灵变量”，以在多个函数调用中添加额外信息以进行不变量测试。一个很好的例子是在上述存入 ERC-4626 代币后对每个 LP 拥有的所有 `shares` 进行求和，并在不变量中使用（ `totalSupply == sumBalanceOf`）。

```solidity
function deposit(uint256 assets) public virtual {
    asset.mint(address(this), assets);

    asset.approve(address(token), assets);

    uint256 shares = token.deposit(assets, address(this));

    sumBalanceOf += shares;
}
```

### 函数级断言

另一个好处是在函数调用发生时执行断言的能力。一个例子是在 `deposit` 函数调用期间断言 LP 的 ERC-20 余额减少了 `assets`，同时他们的 LP 代币余额增加了 `shares`。这样，处理程序函数类似于模糊测试，因为它们可以接受模糊输入，执行状态更改，并在状态更改前/后进行断言。

```solidity
function deposit(uint256 assets) public virtual {
    asset.mint(address(this), assets);

    asset.approve(address(token), assets);

    uint256 beforeBalance = asset.balanceOf(address(this));

    uint256 shares = token.deposit(assets, address(this));

    assertEq(asset.balanceOf(address(this)), beforeBalance - assets);

    sumBalanceOf += shares;
}
```

### 有界/无界函数

此外，使用处理程序，可以将输入参数限定为合理预期的值，以便在 `foundry.toml` 中的 `fail_on_revert` 设置为 `true`。这可以通过 `forge-std` 中的 `bound()` 辅助函数来实现。这确保了模糊器进行的每个函数调用必须对协议成功才能使测试通过。这对于测试协议以期望的方式进行测试非常有用。

```solidity
function deposit(uint256 assets) external {
    assets = bound(assets, 0, 1e30);

    asset.mint(address(this), assets);

    asset.approve(address(token), assets);

    uint256 beforeBalance = asset.balanceOf(address(this));

    uint256 shares = token.deposit(assets, address(this));

    assertEq(asset.balanceOf(address(this)), beforeBalance - assets);

    sumBalanceOf += shares;
}
```

这也可以通过从专用的 “无界” 处理程序合约中继承非有界函数来实现，这些函数可用于 `fail_on_revert = false` 测试。这种类型的测试也很有用，因为它可以暴露对 `bound` 函数使用所做的假设的问题。

```solidity
// Unbounded
function deposit(uint256 assets) public virtual {
    asset.mint(address(this), assets);

    asset.approve(address(token), assets);

    uint256 beforeBalance = asset.balanceOf(address(this));

    uint256 shares = token.deposit(assets, address(this));

    assertEq(asset.balanceOf(address(this)), beforeBalance - assets);

    sumBalanceOf += shares;
}
```

```solidity
// Bounded
function deposit(uint256 assets) external {
    assets = bound(assets, 0, 1e30);

    super.deposit(assets);
}
```

### 操作者管理

在上述函数调用中，可以看到 `address(this)` 是 ERC-4626 合约中唯一的存款人，这不是其预期用途的真实表示。通过在 `forge-std` 中利用 `prank` 作弊码，每个处理程序可以管理一组操作者，并使用它们从不同的 `msg.sender` 地址执行相同的函数调用。这可以通过以下修饰符实现：

```solidity
address[] public actors;

address internal currentActor;

modifier useActor(uint256 actorIndexSeed) {
    currentActor = actors[bound(actorIndexSeed, 0, actors.length - 1)];
    vm.startPrank(currentActor);
    _;
    vm.stopPrank();
}
```

使用多个操作者还允许更精细的幽灵变量使用。这在下面的函数中进行了演示：

```solidity
// Unbounded
function deposit(
    uint256 assets,
    uint256 actorIndexSeed
) public virtual useActor(actorIndexSeed) {
    asset.mint(currentActor, assets);

    asset.approve(address(token), assets);

    uint256 beforeBalance = asset.balanceOf(address(this));

    uint256 shares = token.deposit(assets, address(this));

    assertEq(asset.balanceOf(address(this)), beforeBalance - assets);

    sumBalanceOf += shares;

    sumDeposits[currentActor] += assets
}
```

```solidity
// Bounded
function deposit(uint256 assets, uint256 actorIndexSeed) external {
    assets = bound(assets, 0, 1e30);

    super.deposit(assets, actorIndexSeed);
}
```
