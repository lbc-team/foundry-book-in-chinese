## 不变量测试联合曲线(Bonding Curve)


### 介绍

本教程将涵盖不变量测试，以 **联合曲线实现** 作为目标示例。所有不变量测试均以使用 `Foundry Invaraint Testing` 功能编写的 Solidity 代码为例。

但是，请注意，本指南仅供教育目的。该代码未经审计。请勿在生产环境中使用。

> 💡 注意：可以在[此处](https://github.com/Ratimon/bonding-curves)找到联合曲线的完整实现，有关不变量测试的更多信息，请查看`不变量测试` [参考资料](../reference/forge/invariant-testing.md) 。

### 快速开始

不变量测试的一般过程是，铸造厂将在目标合约中使用随机输入调用一系列预定义的 **操作**。

这将运行多次以确保不变量的正确性。

> 💡 注意：使用随机输入进行操作调用的过程称为 **模糊测试**。

> 💡 注意：生成和运行一系列函数调用的次数称为 **运行**。

在每次单独运行之后，将检查系统是否满足定义的 **不变量**。

关键考虑因素是定义这些内容：

1. **不变量**（一组始终为真的属性）

2. **操作**（每次运行期间可能发生的一系列事情）

要开始，我们将专注于此[存储库](https://github.com/Ratimon/bonding-curves)中的以下目录：

```
.
├── Makefile
├── foundry.toml
└── test
    ├── invariant
```

在本指南中，我们可以通过运行以下命令来运行模糊测试活动：

```sh
make invariant-LinearBondingCurve
```

> 💡 注意：可以在 [ `Makefile`](https://github.com/Ratimon/bonding-curves/blob/master/Makefile) 中找到此教程的其他命令。

我注意到不变量测试的默认配置（在 [ `foundry.toml`](https://github.com/Ratimon/bonding-curves/blob/master/foundry.toml) 中）如下：

```toml
[invariant]
runs          = 1000    # The number of times to run the invariant tests
depth         = 100   # The number of random action calls to make in the invariant tests
fail_on_revert = false
```


### 定义不变量

要指定不变量，我们需要构建关于可能达到的不正确状态的思维模型。

> 💡 注意：不变量是关于某事物（在我们的情况下是一个合约）的逻辑断言的术语，它始终为真。

我们可以定义两种不变量，包括：

1. 函数级别的不变量

- 它们应该是无状态的，或者不太依赖于我们的目标系统。
- 它们可以以孤立的方式进行测试。

> 💡 注意：一个显著的例子可能是将代币存入合约。

2. 系统级别的不变量

- 它们应该是有状态的，或者依赖于智能合约状态。
- 它们依赖于整个系统及其相关的部署逻辑。

> 💡 注意：例如，ERC20 的系统级不变量是 ERC20 代币的总数始终等于或大于所有代币持有者的个体 ERC20 代币余额之和。


### 定义系统级别的不变量

在我们的情况下，用于定义不变量的状态变量（在 [`BondingCurve.sol`](https://github.com/Ratimon/bonding-curves/blob/master/src/bondingcurves/BondingCurve.sol) 中找到）如下：

```solidity
    /** ... */
    abstract contract BondingCurve is IBondingCurve, Initializable, Pausable, Ownable2Step, Timed {
        /** ... */
        /**
        * @notice the total amount of sale token purchased on bonding curve
        *
        */
        UD60x18 public override totalPurchased;

        /**
        * @notice the cap on how much sale token can be minted by the bonding curve
        *
        */
        UD60x18 public override cap;

        /**
        * @notice returns how close to the cap we are
        *
        */
        function availableToSell() public view override returns (UD60x18) {
            return cap.sub(totalPurchased);
        }

        /**
        * @notice balance of accepted token the bonding curve
        * @return the amount of accepted token held in contract and ready to be allocated
        *
        */
        function reserveBalance() public view virtual override returns (UD60x18) {
            return ud(acceptedToken.balanceOf(address(this)));
        }

    /** ... */

    }

```

然后，我们可以在 [`LinearBondingCurve.invariants.t.sol`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/LinearBondingCurve.invariants.t.sol) 中指定并编写断言，如下所示：

1. 不变量 1：totalPurchased + availableToSell = cap

```solidity

    function invariant_totalPurchasedPlusAvailableToSell_eq_cap() public {
        assertEq(
            unwrap(linearBondingCurve.totalPurchased().add(linearBondingCurve.availableToSell())),
            unwrap(linearBondingCurve.cap())
        );
    }

```

2. 不变量 2：availableToSell >= 0

```solidity

    function invariant_AvailableToSell_gt_zero() public {
        assertGt(unwrap(linearBondingCurve.availableToSell()), 0);
    }


```

3. 不变量 3：availableToSell = 联合曲线合约中 ERC20 代币数量

```solidity
    function invariant_AvailableToSell_eq_saleTokenBalance() public {
        assertEq(unwrap(linearBondingCurve.availableToSell()), IERC20(saleToken).balanceOf(address(linearBondingCurve)));
    }
```

4. 不变量 4：Poolbalance =  y = f(x = currentTokenPurchased) =  slope/2 * (currentTokenPurchased)^2 + initialPrice * (currentTokenPurchased)

```solidity
    function invariant_Poolbalance_eq_saleTokenBalance() public {
        UD60x18 acceptedTokenSupply = ud(IERC20(acceptedToken).balanceOf(address(linearBondingCurve)));
        assertEq(
            unwrap(LinearCurve(address(linearBondingCurve)).getPoolBalance(acceptedTokenSupply)),
            unwrap(linearBondingCurve.totalPurchased())
        );
    }
```


### 定义操作逻辑和函数级别的不变量

好了！！我们已经定义了一些系统级别的不变量。接下来的步骤是指定如何执行操作和相关交易序列以打破定义的不变量。

要探索的高级内容在 [`LinearBondingCurve.invariants.t.sol`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/LinearBondingCurve.invariants.t.sol) 中，配置如下：

```solidity

import {InvariantOwner} from "./handlers/Owner.sol";
import {InvariantBuyerManager} from "./handlers/Buyer.sol";
import {Warper} from "./handlers/Warper.sol";

contract LinearBondingCurveInvariants is StdInvariant, Test, ConstantsFixture, DeploymentLinearBondingCurve {
    InvariantOwner internal _owner;
    InvariantBuyerManager internal _buyerManager;
    Warper internal _warper;

    /** ... */

        function setUp() public override {
            /** ... */
            _buyerManager =
                new InvariantBuyerManager(address(linearBondingCurve), address(acceptedToken),  address(saleToken) );
            _warper = new Warper(address(linearBondingCurve));
            _owner = new InvariantOwner(address(linearBondingCurve), address(acceptedToken), address(saleToken), staticTime);

            /** ... */

            vm.startPrank(address(_owner));
            Ownable2Step(address(linearBondingCurve)).acceptOwnership();
            vm.stopPrank();

            vm.startPrank(deployer);
            bytes4[] memory selectors = new bytes4[](1);
            selectors[0] = InvariantBuyerManager.purchase.selector;

            // Performs random purchase() calls
            targetSelector(FuzzSelector({addr: address(_buyerManager), selectors: selectors}));
            targetContract(address(_buyerManager));

            selectors[0] = Warper.warp.selector;
            // Performs random warps forward in time
            targetSelector(FuzzSelector({addr: address(_warper), selectors: selectors}));
            targetContract(address(_warper));

            selectors[0] = InvariantOwner.allocate.selector;
            // Performs random allocate() calls
            targetSelector(FuzzSelector({addr: address(_owner), selectors: selectors}));
            targetContract(address(_owner));

            _buyerManager.createBuyer();
            vm.stopPrank();
        }
    /** ... */
    }

```

总之，我们执行随机的 `purchase()` 调用，随机的时间向前 `warps`，以及随机的 `allocate()` 调用。

这是通过 **不变量测试辅助函数**（包括**targetContract(address newTargetedContract_)** 和 **targetSelector(FuzzSelector memory newTargetedSelector_)**）设置的。

> 💡 注意：更多细节在 `foundry 文档` 的 [`不变量测试辅助函数`](https://book.getfoundry.sh/forge/invariant-testing#invariant-test-helper-functions) 部分中有详细说明。

我们可以将 **Foundry Fuzzer** 视为外部拥有的账户，将 **Handler** 视为智能合约包装器，其中包括一组与我们的目标合约交互的操作。

这些处理程序位于 [`test/invariant/handlers`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/handlers) 中，如下所示：

1.  [`Buyer.sol`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/handlers/Buyer.sol)：执行随机的 `purchase()`

我们可以将这组智能合约视为具有外部利益相关者属性。通常情况下，我们希望模糊器生成多个买家。现在，我们将 **InvariantBuyerManager** 定义如下。

可以看到，在我们的 `setup()` 中调用了 **createBuyer()**，以便我们从 **InvariantBuyerManager** 生成一个买家（**InvariantBuyer**）。

```solidity

contract InvariantBuyerManager is Test {

    /** ... */

    InvariantBuyer[] public buyers;

    /** ... */

    function createBuyer() external {
        InvariantBuyer buyer = new InvariantBuyer(_bondingCurve, _underlyingAcceptedToken, _underlyingSaleToken);
        buyers.push(buyer);
    }

    /** ... */

}
```

然后，生成的买家（**InvariantBuyer**）应该从联合曲线购买代币。现在，我们将编写**函数级别的不变量**。

特别是，我们希望确保在每次购买后，外部智能合约的余额状态（在我们的情况下是 ERC20 代币）都得到正确更新。实现如下：

```solidity

/** ... */

contract InvariantBuyer is Test {
    LinearBondingCurve internal _bondingCurve;
    MockERC20 internal _underlyingAcceptedToken;
    MockERC20 internal _underlyingSaleToken;

    constructor(address bondingCurve_, address underlyingBuyToken_, address underlyingSaleToken_) {
        _bondingCurve = LinearBondingCurve(bondingCurve_);
        _underlyingAcceptedToken = MockERC20(underlyingBuyToken_);
        _underlyingSaleToken = MockERC20(underlyingSaleToken_);
    }

    function purchase(uint256 amount_) external {
        amount_ = bound(amount_, 1, 1e29); // 100 billion at WAD precision
        uint256 startingBuyBalance = _underlyingAcceptedToken.balanceOf(address(this));
        uint256 startingSaleBalance = _underlyingSaleToken.balanceOf(address(this));
        uint256 saleAmountOut = unwrap(_bondingCurve.calculatePurchaseAmountOut(ud(amount_)));
        deal({token: address(_underlyingAcceptedToken), to: address(this), give: amount_});
        _underlyingAcceptedToken.approve(address(_bondingCurve), amount_);
        _bondingCurve.purchase(address(this), amount_);
        // Ensure successful purchase
        assertEq(_underlyingAcceptedToken.balanceOf(address(this)), startingBuyBalance - amount_);
        assertEq(_underlyingSaleToken.balanceOf(address(this)), startingSaleBalance + saleAmountOut);
    }
}

/** ... */

```


2.  [`Owner.sol`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/handlers/Owner.sol)：执行随机的 `allocate()`

同样，我们要确保在将代币分配给发行人后，外部合约（可接受的代币）的余额状态得到正确更新。实现如下：

```solidity

/** ... */

contract InvariantOwner is Test {

    /** ... */

    function allocate(uint256 amount_) external countCall("allocate") {
        vm.warp(staticTime + 3 weeks);
        amount_ = bound(amount_, 0, _underlyingAcceptedToken.balanceOf(address(_bondingCurve)));
        uint256 startingBuyBalance = _underlyingAcceptedToken.balanceOf(address(this));
        _bondingCurve.allocate(amount_, address(this));
        assertEq(_underlyingAcceptedToken.balanceOf(address(this)), startingBuyBalance + amount_);
    }

    /** ... */

}

/** ... */

```

3.  [`Warper.sol`](https://github.com/Ratimon/bonding-curves/blob/master/test/invariant/handlers/Warper.sol)：执行随机的时间向前 `warps`。

由于该系统涉及时间相关的逻辑，如果销售期尚未结束，发行人将无法分配代币。这意味着没有 `warps` 处理程序的情况下，随机的 `allocate()` 将始终被还原。

为此，我们使用 `Foundry 的作弊码`（**vm.warp(uint256)**）来处理。

```solidity

/** ... */

contract Warper is CommonBase, StdCheats, StdUtils {

    /** ... */

    function warp(uint256 warpTime_) external countCall("warp") {
        vm.warp(block.timestamp + bound(warpTime_, 2 weeks, 3 weeks));
    }

    /** ... */

}

/** ... */

```


### 进一步探索！！！

> 💡 注意：我们承认并从项目 [PaulRBerg/prb-math](https://github.com/PaulRBerg/prb-math) 和 [maple-labs/revenue-distribution-token](https://github.com/maple-labs/revenue-distribution-token)中获得灵感并使用它们。

> 💡 参考资料：我们还承认了 [Invariant Testing WETH With Foundry](https://mirror.xyz/horsefacts.eth/Jex2YVaO65dda6zEyfM_-DXlXhOWCAoSpOx5PLocYgw) 和 [Invariant Testing — Enter The Matrix](https://betterprogramming.pub/invariant-testing-enter-the-matrix-c71363dea37e)中的见解性技术写作。