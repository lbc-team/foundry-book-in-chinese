## 测试 EIP-712 签名

### 介绍

[EIP-712](https://eips.ethereum.org/EIPS/eip-712) 引入了在链下签署交易的功能，其他用户稍后可以在链上执行交易。 一个常见的例子是 [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) 无需 Gas 代币批准。

传统上，设置用户或合约 allowance 以从所有者的余额中转移 ERC-20 代币需要所有者提交链上批准。 由于这被证明是糟糕的用户体验，DAI 引入了 ERC-20 `permit`（后来标准化为 EIP-2612），允许所有者签署 _链下_ 批准，支出者（或其他任何人！）可以在 `transferFrom` 之前提交到链上。

本指南将涵盖使用 Foundry 在 Solidity 中测试此模式。

### 开始

首先，我们将介绍基本的代币转移：

- 所有者（Owner）在链下签署批准
- 消费者（Spender）在链上调用 `permit` 和 `transferFrom`

我们将使用 [Solmate 的 ERC-20](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC20.sol)，因为随附了 EIP-712 和 EIP-2612 组合。 如果您还没有浏览完整合约，请看一眼 - 这里是 `permit` 实现的：

```solidity
    /*//////////////////////////////////////////////////////////////
                             EIP-2612 LOGIC
    //////////////////////////////////////////////////////////////*/

    function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public virtual {
        require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

        // Unchecked because the only math done is incrementing
        // the owner's nonce which cannot realistically overflow.
        unchecked {
            address recoveredAddress = ecrecover(
                keccak256(
                    abi.encodePacked(
                        "\x19\x01",
                        DOMAIN_SEPARATOR(),
                        keccak256(
                            abi.encode(
                                keccak256(
                                    "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
                                ),
                                owner,
                                spender,
                                value,
                                nonces[owner]++,
                                deadline
                            )
                        )
                    )
                ),
                v,
                r,
                s
            );

            require(recoveredAddress != address(0) && recoveredAddress == owner, "INVALID_SIGNER");

            allowance[recoveredAddress][spender] = value;
        }

        emit Approval(owner, spender, value);
    }
```

我们还将使用自定义的 `SigUtils` 合约来帮助创建、散列和签署链下批准。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

contract SigUtils {
    bytes32 internal DOMAIN_SEPARATOR;

    constructor(bytes32 _DOMAIN_SEPARATOR) {
        DOMAIN_SEPARATOR = _DOMAIN_SEPARATOR;
    }

    // keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
    bytes32 public constant PERMIT_TYPEHASH =
        0x6e71edae12b1b97f4d1f60370fef10105fa2faae0126114a169c64845d6126c9;

    struct Permit {
        address owner;
        address spender;
        uint256 value;
        uint256 nonce;
        uint256 deadline;
    }

    // computes the hash of a permit
    function getStructHash(Permit memory _permit)
        internal
        pure
        returns (bytes32)
    {
        return
            keccak256(
                abi.encode(
                    PERMIT_TYPEHASH,
                    _permit.owner,
                    _permit.spender,
                    _permit.value,
                    _permit.nonce,
                    _permit.deadline
                )
            );
    }

    // computes the hash of the fully encoded EIP-712 message for the domain, which can be used to recover the signer
    function getTypedDataHash(Permit memory _permit)
        public
        view
        returns (bytes32)
    {
        return
            keccak256(
                abi.encodePacked(
                    "\x19\x01",
                    DOMAIN_SEPARATOR,
                    getStructHash(_permit)
                )
            );
    }
}
```


### 处理动态值

虽然上面在 getStructHash()函数中传递的 Permit 结构体不包含任何动态值类型，但如果您使用它们，重要的是要记住，'bytes' 和 'string' 类型必须编码为它们内容的 'keccak256' 哈希。有关 [EIP 712 规范的更多信息，请参见此处](https://github.com/ethereum/EIPs/blob/8061f8e2243eaae829d1fa91f7a763c889aca371/EIPS/eip-712.md?plain=1#L135) 。


**设置**

- 使用代币的 EIP-712 域分隔符部署模拟 ERC-20 代币和 `SigUtils` 助手
- 创建私钥来模拟所有者和消费者
- 使用 `vm.addr` [cheatcode](https://book.getfoundry.sh/cheatcodes/addr.html) 推导他们的地址
- 为所有者铸造一个测试代币

```solidity
contract ERC20Test is Test {
    MockERC20 internal token;
    SigUtils internal sigUtils;

    uint256 internal ownerPrivateKey;
    uint256 internal spenderPrivateKey;

    address internal owner;
    address internal spender;

    function setUp() public {
        token = new MockERC20();
        sigUtils = new SigUtils(token.DOMAIN_SEPARATOR());

        ownerPrivateKey = 0xA11CE;
        spenderPrivateKey = 0xB0B;

        owner = vm.addr(ownerPrivateKey);
        spender = vm.addr(spenderPrivateKey);

        token.mint(owner, 1e18);
    }
```

**测试：`permit`**

- 为消费者创建批准
- 使用 `sigUtils.getTypedDataHash` 计算其摘要
- 使用带有所有者私钥的 `vm.sign` [cheatcode](https://book.getfoundry.sh/cheatcodes/sign.html) 对摘要进行签名
- 存储签名的 `uint8 v, bytes32 r, bytes32 s`
- 调用 `permit` 并带有已签署的许可证和签名以执行链上批准

```solidity
    function test_Permit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        assertEq(token.allowance(owner, spender), 1e18);
        assertEq(token.nonces(owner), 1);
    }
```

- 确保因截止日期过期、签名者无效、随机数无效和签名重播而导致的调用失败

```solidity
    function testRevert_ExpiredPermit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: token.nonces(owner),
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        vm.warp(1 days + 1 seconds); // fast forward one second past the deadline

        vm.expectRevert("PERMIT_DEADLINE_EXPIRED");
        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );
    }

    function testRevert_InvalidSigner() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: token.nonces(owner),
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(spenderPrivateKey, digest); // spender signs owner's approval

        vm.expectRevert("INVALID_SIGNER");
        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );
    }

    function testRevert_InvalidNonce() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: 1, // owner nonce stored on-chain is 0
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        vm.expectRevert("INVALID_SIGNER");
        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );
    }

    function testRevert_SignatureReplay() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        vm.expectRevert("INVALID_SIGNER");
        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );
    }
```

**测试：`transferFrom`**

- 为消费者创建、签署和执行批准
- 使用 `vm.prank` [cheatcode](https://book.getfoundry.sh/cheatcodes/prank.html) 调用 `tokenTransfer` 作为消费者来执行 transfer

```solidity
    function test_TransferFromLimitedPermit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 1e18,
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        vm.prank(spender);
        token.transferFrom(owner, spender, 1e18);

        assertEq(token.balanceOf(owner), 0);
        assertEq(token.balanceOf(spender), 1e18);
        assertEq(token.allowance(owner, spender), 0);
    }

    function test_TransferFromMaxPermit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: type(uint256).max,
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        vm.prank(spender);
        token.transferFrom(owner, spender, 1e18);

        assertEq(token.balanceOf(owner), 0);
        assertEq(token.balanceOf(spender), 1e18);
        assertEq(token.allowance(owner, spender), type(uint256).max);
    }
```

- 确保 allowance 无效和 balance 无效，call 会失败

```solidity
    function testFail_InvalidAllowance() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 5e17, // approve only 0.5 tokens
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        vm.prank(spender);
        token.transferFrom(owner, spender, 1e18); // attempt to transfer 1 token
    }

    function testFail_InvalidBalance() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: spender,
            value: 2e18, // approve 2 tokens
            nonce: 0,
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        token.permit(
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        vm.prank(spender);
        token.transferFrom(owner, spender, 2e18); // attempt to transfer 2 tokens (owner only owns 1)
    }
```

### Bundled 示例

这是 [模拟合约](https://github.com/kulkarohan/deposit/blob/main/src/Deposit.sol) 的一部分，它只存入 ERC-20 代币。 请注意，`deposit` 需要初步的 `approve` 或 `permit` tx 才能转移代币，而 `depositWithPermit` 设置限额 _并_ 在单个 tx 中转移代币。

```solidity
    ///                                                          ///
    ///                           DEPOSIT                        ///
    ///                                                          ///

    /// @notice Deposits ERC-20 tokens (requires pre-approval)
    /// @param _tokenContract The ERC-20 token address
    /// @param _amount The number of tokens
    function deposit(address _tokenContract, uint256 _amount) external {
        ERC20(_tokenContract).transferFrom(msg.sender, address(this), _amount);

        userDeposits[msg.sender][_tokenContract] += _amount;

        emit TokenDeposit(msg.sender, _tokenContract, _amount);
    }

    ///                                                          ///
    ///                      DEPOSIT w/ PERMIT                   ///
    ///                                                          ///

    /// @notice Deposits ERC-20 tokens with a signed approval
    /// @param _tokenContract The ERC-20 token address
    /// @param _amount The number of tokens to transfer
    /// @param _owner The user signing the approval
    /// @param _spender The user to transfer the tokens (ie this contract)
    /// @param _value The number of tokens to appprove the spender
    /// @param _deadline The timestamp the permit expires
    /// @param _v The 129th byte and chain id of the signature
    /// @param _r The first 64 bytes of the signature
    /// @param _s Bytes 64-128 of the signature
    function depositWithPermit(
        address _tokenContract,
        uint256 _amount,
        address _owner,
        address _spender,
        uint256 _value,
        uint256 _deadline,
        uint8 _v,
        bytes32 _r,
        bytes32 _s
    ) external {
        ERC20(_tokenContract).permit(
            _owner,
            _spender,
            _value,
            _deadline,
            _v,
            _r,
            _s
        );

        ERC20(_tokenContract).transferFrom(_owner, address(this), _amount);

        userDeposits[_owner][_tokenContract] += _amount;

        emit TokenDeposit(_owner, _tokenContract, _amount);
    }
```

**设置**

- 使用代币的 EIP-712 域分隔符部署 `Deposit` 合约、模拟 ERC-20 代币和 `SigUtils` 助手
- 创建一个私钥来模拟所有者（支出者现在是 `Deposit` 地址）
- 使用 `vm.addr` [cheatcode](https://book.getfoundry.sh/cheatcodes/addr.html) 推导出所有者地址
- 为所有者铸造一个测试代币

```solidity
contract DepositTest is Test {
    Deposit internal deposit;
    MockERC20 internal token;
    SigUtils internal sigUtils;

    uint256 internal ownerPrivateKey;
    address internal owner;

    function setUp() public {
        deposit = new Deposit();
        token = new MockERC20();
        sigUtils = new SigUtils(token.DOMAIN_SEPARATOR());

        ownerPrivateKey = 0xA11CE;
        owner = vm.addr(ownerPrivateKey);

        token.mint(owner, 1e18);
    }
```
**测试：`depositWithPermit`**

- 为“存款”合约创建批准
- 使用 `sigUtils.getTypedDataHash` 计算其摘要
- 使用带有所有者私钥的 `vm.sign` [cheatcode](https://book.getfoundry.sh/cheatcodes/sign.html) 对摘要进行签名
- 存储签名的`uint8 v, bytes32 r, bytes32 s`
   - _注意：_ 可以通过 `bytes signature = abi.encodePacked(r, s, v)` 转换为字节
- 使用已签署的批准和签名调用 `depositWithPermit` 将代币转移到合约中

```solidity
    function test_DepositWithLimitedPermit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: address(deposit),
            value: 1e18,
            nonce: token.nonces(owner),
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        deposit.depositWithPermit(
            address(token),
            1e18,
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        assertEq(token.balanceOf(owner), 0);
        assertEq(token.balanceOf(address(deposit)), 1e18);

        assertEq(token.allowance(owner, address(deposit)), 0);
        assertEq(token.nonces(owner), 1);

        assertEq(deposit.userDeposits(owner, address(token)), 1e18);
    }

    function test_DepositWithMaxPermit() public {
        SigUtils.Permit memory permit = SigUtils.Permit({
            owner: owner,
            spender: address(deposit),
            value: type(uint256).max,
            nonce: token.nonces(owner),
            deadline: 1 days
        });

        bytes32 digest = sigUtils.getTypedDataHash(permit);

        (uint8 v, bytes32 r, bytes32 s) = vm.sign(ownerPrivateKey, digest);

        deposit.depositWithPermit(
            address(token),
            1e18,
            permit.owner,
            permit.spender,
            permit.value,
            permit.deadline,
            v,
            r,
            s
        );

        assertEq(token.balanceOf(owner), 0);
        assertEq(token.balanceOf(address(deposit)), 1e18);

        assertEq(token.allowance(owner, address(deposit)), type(uint256).max);
        assertEq(token.nonces(owner), 1);

        assertEq(deposit.userDeposits(owner, address(token)), 1e18);
    }
```

- 确保无效的 `permit` 和 `transferFrom` 调用失败，如前所示

### 长篇大论

使用 Foundry 作弊码 `addr`、`sign` 和 `prank` 来测试 Foundry 中的 EIP-712 签名。

所有源代码都可以在 [此处](https://github.com/kulkarohan/deposit) 找到。
