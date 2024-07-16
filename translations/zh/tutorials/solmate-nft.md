## 使用 Solmate 创建 NFT

本教程将引导您使用 Foundry 和 [Solmate](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC721.sol) 创建兼容 OpenSea 的 NFT。 可以在 [此处](https://github.com/FredCoen/nft-tutorial) 找到本教程的完整实现。

##### 本教程仅用于说明目的，并按原样提供。 本教程未经审核或全面测试。 不应在生产环境中使用本教程中的任何代码。

### 创建项目并安装依赖

按照 [入门部分](../getting-started/installation.html) 中列出的步骤开始设置 Foundry 项目。 我们还将为其 ERC721 实现安装 Solmate，以及一些 OpenZeppelin 实用程序库。 通过从项目的根目录运行以下命令来安装依赖项：

```bash
forge install transmissions11/solmate Openzeppelin/openzeppelin-contracts
```

这些依赖项将作为 git 子模块添加到您的项目中。

如果您正确地按照说明进行操作，您的项目结构应该如下所示：

![Project structure](../images/nft-tutorial/nft-tutorial-project-structure.png)


### 实现一个基本的 NFT

然后我们将 `src/Contract.sol` 中的样板合约重命名为  `src/NFT.sol` 并替换代码：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.10;

import "solmate/tokens/ERC721.sol";
import "openzeppelin-contracts/contracts/utils/Strings.sol";

contract NFT is ERC721 {
    uint256 public currentTokenId;

    constructor(
        string memory _name,
        string memory _symbol
    ) ERC721(_name, _symbol) {}

    function mintTo(address recipient) public payable returns (uint256) {
        uint256 newItemId = ++currentTokenId;
        _safeMint(recipient, newItemId);
        return newItemId;
    }

    function tokenURI(uint256 id) public view virtual override returns (string memory) {
        return Strings.toString(id);
    }
}
```

让我们来看看 NFT 的这个非常基本的实现。 我们首先从我们的 git 子模块中导入两个合约。 我们导入了 solmate 的 ERC721 标准 Gas 优化实现 ，我们的 NFT 合约将继承该标准。 我们的构造函数为我们的 NFT 获取 `_name` 和 `_symbol` 参数，并将它们传递给父 ERC721 实现的构造函数。 最后，我们实现了允许任何人铸造 NFT 的 `mintTo` 函数。 此函数递增 `currentTokenId` 并使用我们父合约的 `_safeMint` 函数。

### 使用 forge 编译部署

要编译 NFT 合约，请运行 `forge build`。 由于映射错误，您可能会遇到构建失败的情况：

```text
Error:
Compiler run failed
error[6275]: ParserError: Source "lib/openzeppelin-contracts/contracts/contracts/utils/Strings.sol" not found: File not found. Searched the following locations: "/PATH/TO/REPO".
 --> src/NFT.sol:5:1:
  |
5 | import "openzeppelin-contracts/contracts/utils/Strings.sol";
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

这可以通过设置正确的重映射来解决。 在您的项目中创建一个文件 `remappings.txt` 并添加以下行

```text
openzeppelin-contracts/=lib/openzeppelin-contracts/
```

（您可以在 [依赖文档](../projects/dependencies.md) 中找到有关重新映射的更多信息。

默认情况下，编译器输出将位于 `out`目录中。 要使用 Forge 部署我们编译好的合约，我们必须为 RPC 端点和我们要用于部署的私钥设置环境变量。

通过运行以下命令设置环境变量：

```bash
export RPC_URL=<你的 RPC 端点>
export PRIVATE_KEY=<你的钱包私钥>
```

设置完成后，您可以通过运行以下命令，同时将相关构造函数参数添加到 NFT 合约来使用 Forge 部署您的 NFT：

```bash
forge create NFT --rpc-url=$RPC_URL --private-key=$PRIVATE_KEY --constructor-args <name> <symbol>
```

如果部署成功，您将看到部署钱包的地址、合约地址以及交易哈希打印到您的终端。

### 从你的合约中铸造 NFT

使用 Foundry 用于与智能合约交互、发送交易和获取链数据的命令行工具 Cast 可以轻松调用 NFT 合约上的函数。 让我们看看如何使用它从我们的 NFT 合约中铸造 NFT。

鉴于您已经在部署期间设置了 RPC 和私钥环境变量，请通过以下方式从您的合约中铸造一个 NFT ：

```bash
cast send --rpc-url=$RPC_URL <contractAddress> "mintTo(address)" <arg> --private-key=$PRIVATE_KEY
```

做得好！ 你刚刚从你的合约中铸造了你的第一个 NFT。 您可以通过运行以下 `cast call` 命令来检查 `currentTokenId` 等于 **1** 的 NFT 的所有者。 您在上面提供的地址应该作为所有者返回。

```bash
cast call --rpc-url=$RPC_URL --private-key=$PRIVATE_KEY <contractAddress> "ownerOf(uint256)" 1
```

### 扩展我们的 NFT 合约功能和测试

让我们通过添加元数据来表示 NFT 的内容来扩展我们的 NFT，并设置铸币价格、最大供应量以及从铸币中提取所收集收益的可能性。 您可以使用以下代码片段替换当前的 NFT 合约：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.10;

import "solmate/tokens/ERC721.sol";
import "openzeppelin-contracts/contracts/utils/Strings.sol";
import "openzeppelin-contracts/contracts/access/Ownable.sol";

error MintPriceNotPaid();
error MaxSupply();
error NonExistentTokenURI();
error WithdrawTransfer();

contract NFT is ERC721, Ownable {

    using Strings for uint256;
    string public baseURI;
    uint256 public currentTokenId;
    uint256 public constant TOTAL_SUPPLY = 10_000;
    uint256 public constant MINT_PRICE = 0.08 ether;

    constructor(
        string memory _name,
        string memory _symbol,
        string memory _baseURI
    ) ERC721(_name, _symbol) {
        baseURI = _baseURI;
    }

    function mintTo(address recipient) public payable returns (uint256) {
        if (msg.value != MINT_PRICE) {
            revert MintPriceNotPaid();
        }
        uint256 newTokenId = ++currentTokenId;
        if (newTokenId > TOTAL_SUPPLY) {
            revert MaxSupply();
        }
        _safeMint(recipient, newTokenId);
        return newTokenId;
    }

    function tokenURI(uint256 tokenId)
        public
        view
        virtual
        override
        returns (string memory)
    {
        if (ownerOf(tokenId) == address(0)) {
            revert NonExistentTokenURI();
        }
        return
            bytes(baseURI).length > 0
                ? string(abi.encodePacked(baseURI, tokenId.toString()))
                : "";
    }

    function withdrawPayments(address payable payee) external onlyOwner {
        uint256 balance = address(this).balance;
        (bool transferTx, ) = payee.call{value: balance}("");
        if (!transferTx) {
            revert WithdrawTransfer();
        }
    }
}
```


除此之外，我们还添加了元数据，可以通过调用 NFT 合约上的 `tokenURI` 方法从任何前端应用程序（如 OpenSea）查询这些元数据。

> **注意**：如果您想在部署时向构造函数提供真实 URL，并托管此 NFT 合约的元数据，请按照[此处](https://docs.opensea.io/docs/part-3-adding-metadata-and-payments-to-your-contract#intro-to-nft-metadata）。

让我们测试一些添加的功能，以确保它按预期工作。 Foundry 通过 Forge 提供了一个极快的 EVM 原生测试框架。

在您的测试文件夹中，将当前的 `Contract.t.sol` 测试文件重命名为 `NFT.t.sol`。 该文件将包含有关 NFT 的 `mintTo` 方法的所有测试。 接下来，将现有的样板代码替换为以下代码：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.10;

import "forge-std/Test.sol";
import "../src/NFT.sol";

contract NFTTest is Test {
    using stdStorage for StdStorage;

    NFT private nft;

    function setUp() public {
        // Deploy NFT contract
        nft = new NFT("NFT_tutorial", "TUT", "baseUri");
    }

    function test_RevertMintWithoutValue() public {
        vm.expectRevert(MintPriceNotPaid.selector);
        nft.mintTo(address(1));
    }

    function test_MintPricePaid() public {
        nft.mintTo{value: 0.08 ether}(address(1));
    }

    function test_RevertMintMaxSupplyReached() public {
        uint256 slot = stdstore
            .target(address(nft))
            .sig("currentTokenId()")
            .find();
        bytes32 loc = bytes32(slot);
        bytes32 mockedCurrentTokenId = bytes32(abi.encode(10000));
        vm.store(address(nft), loc, mockedCurrentTokenId);
        vm.expectRevert(MaxSupply.selector);
        nft.mintTo{value: 0.08 ether}(address(1));
    }

    function test_RevertMintToZeroAddress() public {
        vm.expectRevert("INVALID_RECIPIENT");
        nft.mintTo{value: 0.08 ether}(address(0));
    }

    function test_NewMintOwnerRegistered() public {
        nft.mintTo{value: 0.08 ether}(address(1));
        uint256 slotOfNewOwner = stdstore
            .target(address(nft))
            .sig(nft.ownerOf.selector)
            .with_key(1)
            .find();

        uint160 ownerOfTokenIdOne = uint160(
            uint256(
                (vm.load(address(nft), bytes32(abi.encode(slotOfNewOwner))))
            )
        );
        assertEq(address(ownerOfTokenIdOne), address(1));
    }

    function test_BalanceIncremented() public {
        nft.mintTo{value: 0.08 ether}(address(1));
        uint256 slotBalance = stdstore
            .target(address(nft))
            .sig(nft.balanceOf.selector)
            .with_key(address(1))
            .find();

        uint256 balanceFirstMint = uint256(
            vm.load(address(nft), bytes32(slotBalance))
        );
        assertEq(balanceFirstMint, 1);

        nft.mintTo{value: 0.08 ether}(address(1));
        uint256 balanceSecondMint = uint256(
            vm.load(address(nft), bytes32(slotBalance))
        );
        assertEq(balanceSecondMint, 2);
    }

    function test_SafeContractReceiver() public {
        Receiver receiver = new Receiver();
        nft.mintTo{value: 0.08 ether}(address(receiver));
        uint256 slotBalance = stdstore
            .target(address(nft))
            .sig(nft.balanceOf.selector)
            .with_key(address(receiver))
            .find();

        uint256 balance = uint256(vm.load(address(nft), bytes32(slotBalance)));
        assertEq(balance, 1);
    }

    function test_RevertUnSafeContractReceiver() public {
        // Adress set to 11, because first 10 addresses are restricted for precompiles
        vm.etch(address(11), bytes("mock code"));
        vm.expectRevert(bytes(""));
        nft.mintTo{value: 0.08 ether}(address(11));
    }

    function test_WithdrawalWorksAsOwner() public {
        // Mint an NFT, sending eth to the contract
        Receiver receiver = new Receiver();
        address payable payee = payable(address(0x1337));
        uint256 priorPayeeBalance = payee.balance;
        nft.mintTo{value: nft.MINT_PRICE()}(address(receiver));
        // Check that the balance of the contract is correct
        assertEq(address(nft).balance, nft.MINT_PRICE());
        uint256 nftBalance = address(nft).balance;
        // Withdraw the balance and assert it was transferred
        nft.withdrawPayments(payee);
        assertEq(payee.balance, priorPayeeBalance + nftBalance);
    }

    function test_WithdrawalFailsAsNotOwner() public {
        // Mint an NFT, sending eth to the contract
        Receiver receiver = new Receiver();
        nft.mintTo{value: nft.MINT_PRICE()}(address(receiver));
        // Check that the balance of the contract is correct
        assertEq(address(nft).balance, nft.MINT_PRICE());
        // Confirm that a non-owner cannot withdraw
        vm.expectRevert("Ownable: caller is not the owner");
        vm.startPrank(address(0xd3ad));
        nft.withdrawPayments(payable(address(0xd3ad)));
        vm.stopPrank();
    }
}

contract Receiver is ERC721TokenReceiver {
    function onERC721Received(
        address operator,
        address from,
        uint256 id,
        bytes calldata data
    ) external override returns (bytes4) {
        return this.onERC721Received.selector;
    }
}

```

测试套件设置为带有 `setUp` 方法的合约，该方法在每个单独的测试之前运行。

如您所见，Forge 提供了许多 [cheatcodes](../cheatcodes/) 来操纵状态以适应您的测试场景。

例如，我们的 `testFailMaxSupplyReached` 测试会检查在达到 NFT 的最大供应量时尝试铸造是否失败。 因此，NFT 合约的 `currentTokenId` 需要通过使用作弊码 `store` 设置最大供应量，这允许您将数据写入您的合约存储槽。 使用[`forge-std`](https://github.com/foundry-rs/forge-std/) 
可以很容易地找到你希望写入的存储槽。 您可以使用以下命令运行测试：

```bash
forge test
```

如果您想练习您的 Forge 技能，请为我们的 NFT 合约的其余方法编写测试。 欢迎将它们 PR 到 [nft-tutorial](https://github.com/FredCoen/nft-tutorial)，您将在其中找到本教程的完整实现。

### 函数调用的 Gas 报告

Foundry 提供有关您的合约的综合 Gas 报告。 对于测试中调用的每个函数，它都会返回最小、平均、中值和最大 Gas 成本。 要打印 Gas 报告，只需运行：

```bash
forge test --gas-report
```

在查看合约中的各种 Gas 优化时，这会派上用场。

让我们来看看我们通过用 Solmate 代替 OpenZeppelin 来实现我们的 ERC721 实现而节省的 Gas。 您可以在[此处](https://github.com/FredCoen/nft-tutorial) 找到使用这两个库的 NFT 实现。 以下是在该存储库上运行 `forge test --gas-report` 时生成的 Gas 报告。

如您所见，我们使用 Solmate 的实施在一次成功的铸造中节省了大约 500 gas（`mintTo` 函数调用的最大 Gas 成本）。

![Gas report solmate NFT](../images/nft-tutorial/gas-report-solmate-nft.png)

![Gas report OZ NFT](../images/nft-tutorial/gas-report-oz-nft.png)

就是这样，我希望这能为您提供如何开始使用 Foundry 的良好实践基础。 我们认为没有比在 solidity 中编写测试更好的方式来深入理解 solidity。 您还将体验到更少的 javascript 和 solidity 之间的上下文切换。 编码愉快！

> 注意：按照 [此处](./solidity-scripting.md) 教程学习如何使用 solidity 脚本部署此处使用的 NFT 合约。
