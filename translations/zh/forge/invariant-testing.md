# 不变性测试
## 概述

不变性测试允许针对预定义合约的预定义函数调用的随机序列测试一组不变性表达式。在执行每个函数调用后，会对所有定义的不变性进行断言。

不变性测试是暴露协议中不正确逻辑的强大工具。由于函数调用序列是随机的，并且具有模糊输入，不变性测试可以暴露边缘情况和高度复杂协议状态中的错误假设和不正确逻辑。

不变性测试活动有两个维度，即 `runs `和 `depth`：
- `runs`：生成和运行函数调用序列的次数。
- `depth`：在给定的 `run` 中进行的函数调用次数。在进行每个函数调用后，都会对所有定义的不变性进行断言。如果函数调用回滚，则 `depth` 计数器仍会递增。

这些以及其他不变性配置方面的内容在 [`此处`](#configuring-invariant-test-execution) 有详细说明。

与在 Foundry 中运行标准测试的方式类似，通过在函数名前加上 `test` 前缀来运行不变性测试，通过在函数名前加上 `invariant` 前缀来表示不变性测试（例如，`function invariant_A()`）。

### 配置不变性测试执行

不变性测试执行由用户可以通过 Forge 配置基元控制的参数来管理。配置可以全局应用或针对每个测试进行应用。有关此主题的详细信息，请参考
 📚 [`全局配置`](../reference/config/testing.md) 和 📚 [`内联配置`](../reference/config/inline-test-config.md)。

## 定义不变性

不变性是应该在模糊测试活动期间始终成立的条件表达式。一个良好的不变性测试套件应该尽可能多地包含不变性，并且可以针对不同的协议状态具有不同的测试套件。

不变性的示例包括：
- 对于 Uniswap，*“xy=k公式始终成立”*。
- 对于 ERC-20 代币，*“所有用户余额的总和等于总供应量”*。

有不同的方法来断言不变性，如下表所述：

<table>
<tr><th>类型</th><th>解释</th><th>示例</th></tr>

<tr>

<td>直接断言</td>
<td>查询协议智能合约并断言值是否符合预期。</td>
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
<td>查询协议智能合约并将其与在测试环境中持久化的值（幽灵变量）进行比较。</td>
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
<td>查询协议智能合约并将其与相同期望逻辑的天真且通常高度耗气的实现进行比较。</td>
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

### 条件不变性

不变性必须在给定模糊测试活动期间始终成立，但这并不意味着它们在每种情况下都必须成立。在特定情况下（例如，在清算期间）可能会引入/移除某些不变性。

不建议在不变性断言中引入条件逻辑，因为它们可能会因为不正确的代码路径而引入错误的阳性结果。例如：

```solidity
function invariant_example() external {
    if (protocolCondition) return;

    assertEq(val1, val2);
}
```

在这种情况下，如果 `protocolCondition == true`，则根本不会断言不变性。有时这可能是期望的行为，但如果 `protocolCondition` 意外地在整个模糊测试活动期间为 true，或者条件本身存在逻辑错误，则可能会引发问题。因此，最好尝试为该条件定义另一个不变性，例如：

```solidity
function invariant_example() external {
    if (protocolCondition) {
        assertLe(val1, val2);
        return;
    };

    assertEq(val1, val2);
}
```

处理协议状态下不同不变性的另一种方法是利用不同场景的专用不变性测试合约。这些场景可以使用 `setUp` 函数进行引导，但更强大的方法是利用 *不变性目标* 来控制模糊器的行为，从而仅产生特定结果（例如，避免清算）。

## 不变性目标

**目标合约**：在给定的不变性测试模糊活动期间将被调用的合约集。这组合约默认为在 `setUp` 函数中部署的所有合约，但可以进行定制以允许更高级的不变性测试。

**目标发送方**：不变性测试模糊器默认情况下会随机选择 `msg.sender` 的值进行模糊测试活动，以模拟系统中的多个参与者。如果需要，可以在 `setUp` 函数中定制发送方集。

**目标选择器**：用于不变性测试的模糊器的函数选择器集。这些可以用于在给定目标合约中使用函数的子集。

**目标工件**：要用于给定合约的期望 ABI。这些可以用于代理合约配置。

**目标工件选择器**：要在给定合约的给定 ABI 中使用的函数选择器的期望子集。这些可以用于代理合约配置。

在目标冲突的情况下，不变性模糊器的优先级为：

`目标选择器 | 目标工件选择器 > 排除合约 | 排除工件 > 目标合约 | 目标工件`

### 函数调用概率分布

这些合约的函数将以随机方式进行模糊测试活动。函数被调用的概率按合约和函数进行了详细说明。

例如：

```text
targetContract1: 50%
├─ function1: 50% (25%)
└─ function2: 50% (25%)

targetContract2: 50%
├─ function1: 25% (12.5%)
├─ function2: 25% (12.5%)
├─ function3: 25% (12.5%)
└─ function4: 25% (12.5%)
```

在设计目标合约时需要注意这一点，因为函数较少的目标合约由于这种概率分布，每个函数被调用的次数会更多。

### 不变性测试辅助函数
不变性测试辅助函数包含在 [`forge-std`](https://github.com/foundry-rs/forge-std/blob/master/src/StdInvariant.sol) 中，以允许可配置的不变性测试设置。以下是这些辅助函数的概述：

| 函数 | 描述 |
|-|-|
| `excludeContract(address newExcludedContract_)` | 将给定地址添加到 `_excludedContracts` 数组中。这组合约明确地从目标合约中排除。|
| `excludeSender(address newExcludedSender_)` | 将给定地址添加到 `_excludedSenders` 数组中。这组地址明确地从目标发送方中排除。 |
| `excludeArtifact(string memory newExcludedArtifact_)` | 将给定字符串添加到 `_excludedArtifacts` 数组中。这组字符串明确地从目标工件中排除。 |
| `targetArtifact(string memory newTargetedArtifact_)` | 将给定字符串添加到 `_targetedArtifacts` 数组中。这组字符串用于目标工件。  |
| `targetArtifactSelector(FuzzSelector memory newTargetedArtifactSelector_)` | 将给定的 `FuzzSelector` 添加到 `_targetedArtifactSelectors` 数组中。这组 `FuzzSelector` 用于目标工件选择器。 |
| `targetContract(address newTargetedContract_)` | 将给定地址添加到 `_targetedContracts` 数组中。这组地址用于目标合约。该数组会覆盖在 `setUp` 期间部署的合约集。 |
| `targetSelector(FuzzSelector memory newTargetedSelector_)` | 将给定的 `FuzzSelector` 添加到 `_targetedSelectors` 数组中。这组 `FuzzSelector` 用于目标合约选择器。 |
| `targetSender(address newTargetedSender_)` | 将给定地址添加到 `_targetedSenders` 数组中。这组地址用于目标发送方。 |
### 目标合约设置

可以使用以下三种方法设置目标合约：
1. 手动添加到 `targetContracts` 数组中的合约将被添加到目标合约集合中。
2. 在 `setUp` 函数中部署的合约将自动添加到目标合约集合中（仅在没有使用选项 1 手动添加合约时有效）。
3. 在 `setUp` 中部署的合约可以从目标合约中 **移除** ，如果它们被添加到 `excludeContracts` 数组中。

## 开放测试

目标合约的默认配置设置为在设置期间部署的所有合约。对于较小的模块和更多的算术合约，这种方法效果很好。例如：

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

可以使用默认目标合约模式部署和测试此合约：

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
        assertGe(foo.val1() + foo.val2(), foo.val1());
    }

}
```

此设置将使用模糊输入以 50%-50%的概率分布调用`foo.addToA()`和`foo.addToB()`。不可避免地，输入将开始引起溢出，并且函数调用将开始回滚。由于不变量测试中的默认配置为`fail_on_revert = false`，这不会导致测试失败。不变量将在模糊测试活动的其余过程中保持不变，测试将通过。输出将类似于：

```text
[PASS] invariant_A() (runs: 50, calls: 10000, reverts: 5533)
[PASS] invariant_B() (runs: 50, calls: 10000, reverts: 5533)
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
