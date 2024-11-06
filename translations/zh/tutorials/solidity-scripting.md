## Solidity 脚本

### 介绍

Solidity 脚本是一种使用 Solidity 以声明方式部署合约的方法，而不是使用限制更多且用户友好度较低的 [`forge create`](../reference/forge/forge-create.md)。

Solidity 脚本就像您在使用 Hardhat 等工具时编写的脚本； Solidity 脚本的不同之处在于它们是用 Solidity 而不是 JavaScript 编写的，并且它们在快速的 Foundry EVM 后端上运行，该后端提供试运行功能。

### 高级概述 

`forge script` 不以同步方式工作。首先，它会从脚本中收集所有交易，然后才会广播它们。它基本上可以分为 4 个阶段：

1. 本地模拟 - 合同脚本在本地 evm 中运行。如果提供了 rpc/fork url，它将在该上下文中执行脚本。从 `vm.broadcast` 和/或 `vm.startBroadcast` 进行的任何**外部调用**（非静态，非内部）都将被追加到列表中。
2. 链上模拟 - 可选。如果提供了 rpc/fork url，则它将按顺序执行上一个阶段收集的所有交易。
3. 广播 - 可选。如果提供了 `--broadcast` 标志，并且前几个阶段已成功，它将广播在步骤 `1` 收集的交易，并在步骤 `2` 模拟。
4. 验证 - 可选。如果提供了 `--verify` 标志，有一个 API 密钥，并且前几个阶段已成功，它将尝试验证合同（例如 etherscan）。

考虑到这个流程，重要的是要意识到，那些行为可能受外部状态/参与者影响的交易，其结果可能与步骤 `2` 模拟的结果不同。例如，前置交易。

### 设置

让我们尝试使用 solidity 脚本部署在 solmate 教程中制作的 NFT 合约。 首先，我们需要通过以下方式创建一个新的 Foundry 项目：

```sh
forge init solidity-scripting
```

由于 solmate 教程中的 NFT 合约继承了 solmate 和 OpenZeppelin 合约，我们必须通过运行以下命令将它们安装为依赖项：

```sh
# Enter the project
cd solidity-scripting

# Install Solmate and OpenZeppelin contracts as dependencies
forge install transmissions11/solmate Openzeppelin/openzeppelin-contracts@v5.0.1
```

接下来，我们必须删除 `src` 文件夹中的 `Counter.sol` 文件并创建另一个名为 `NFT.sol` 的文件。 为此，你可以运行：

```sh
rm src/Counter.sol test/Counter.t.sol script/Counter.s.sol && touch src/NFT.sol && ls src
```

![set up commands](https://img.learnblockchain.cn/pics/20230309091416.png)


完成后，你应该打开你喜欢的代码编辑器并将下面的代码复制到 `NFT.sol` 文件中。

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.10;

import {ERC721} from "solmate/tokens/ERC721.sol";
import {Ownable} from "openzeppelin-contracts/contracts/access/Ownable.sol";
import {Strings} from "openzeppelin-contracts/contracts/utils/Strings.sol";

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
    ) ERC721(_name, _symbol) Ownable(msg.sender) {
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

现在，让我们尝试编译我们的合约以确保一切正常。

```sh
forge build
```

如果您的输出看起来像这样，则合约已成功编译。
![compile successful](https://img.learnblockchain.cn/pics/20230309091427.png)

### 部署我们的合约

我们将把 `NFT` 合约部署到 Sepolia 测试网，但为此我们需要对 Foundry 进行一些配置，比如设置 Sepolia RPC URL、一个有 Sepolia Eth 资金的账户的私钥，以及用于验证 NFT 合约的 Etherscan 密钥。

> 💡 注意：你可以在[这里](https://sepoliafaucet.com/)获取一些 Sepolia 测试网 ETH。

#### 环境配置

完成所有这些后，创建一个 `.env` 文件并添加变量。 Foundry 会自动加载项目目录中的 `.env` 文件。

 `.env` 文件应遵循以下格式：

```sh
SEPOLIA_RPC_URL=
PRIVATE_KEY=
ETHERSCAN_API_KEY=
```

我们现在需要编辑 `foundry.toml` 文件。 项目的根目录中应该已经有一个。

将以下行添加到文件末尾：

```toml
[rpc_endpoints]
sepolia = "${SEPOLIA_RPC_URL}"

[etherscan]
sepolia = { key = "${ETHERSCAN_API_KEY}" }
```

这将为 Sepolia 测试网创建一个 [RPC 别名](../cheatcodes/rpc.md) 并加载 Etherscan 的 API 密钥。

#### 编写脚本

接下来，我们必须创建一个文件夹并将其命名为 `script`，并在其中创建一个名为 `NFT.s.sol` 的文件。 这是我们将创建部署脚本本身的地方。

`NFT.s.sol` 的内容应该是这样的：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Script} from "forge-std/Script.sol";
import {NFT} from "../src/NFT.sol";

contract MyScript is Script {
    function run() external {
        uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
        vm.startBroadcast(deployerPrivateKey);

        NFT nft = new NFT("NFT_tutorial", "TUT", "baseUri");

        vm.stopBroadcast();
    }
}
```

现在让我们通读代码并弄清楚它的实际含义和作用。

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;
```

请记住，即使它是一个脚本，它仍然像智能合约一样工作，但从未部署过，所以就像任何其他用 Solidity 编写的智能合约一样，必须指定 `pragma version`。

```solidity
import {Script} from "forge-std/Script.sol";
import {NFT} from "../src/NFT.sol";
```

就像我们在编写测试时可能会导入 Forge Std 来获取测试实用程序一样，Forge Std 也提供了一些我们在这里导入的脚本实用程序。

下一行只是导入`NFT`合约。

```solidity
contract MyScript is Script {
```

我们创建了一个名为 `MyScript` 的合约，它从 Forge Std 继承了 `Script`。

```solidity
 function run() external {
```

默认情况下，脚本是通过调用名为 `run` 的函数（我们的入口点）来执行的。

```solidity
uint256 deployerPrivateKey = vm.envUint("PRIVATE_KEY");
```

这会从我们的 .env 文件中加载私钥。 **注意：** 在 `.env` 文件中公开私钥并将它们加载到程序中时必须小心。 这仅建议与非特权部署者一起使用或用于本地/测试设置。 对于生产设置，请查看 Foundry 支持的各种 [钱包选项](../reference/forge/forge-script.md#wallet-options---raw)。


```solidity
vm.startBroadcast(deployerPrivateKey);
```

这是一个特殊的作弊码，用于记录我们的主脚本合约进行的调用和合约创建。 我们传递 `deployerPrivateKey` 以指示它使用该密钥来签署交易。 稍后，我们将广播这些交易以部署我们的 NFT 合约。
```solidity
 NFT nft = new NFT("NFT_tutorial", "TUT", "baseUri");
```

在这里，我们刚刚创建了我们的 NFT 合约。因为我们在这行代码之前调用了 `vm.startBroadcast()`，所以合约的创建将被 Forge 记录下来，如前所述，我们可以将交易广播以在链上部署合约。广播交易日志将默认存储在 `broadcast` 目录中。你可以通过在 `foundry.toml` 文件中设置 [`broadcast`](../reference/config/project.md#broadcast) 来更改日志的位置。

广播发送者的确定顺序如下：

1. 如果提供了 `--sender` 参数，则使用该地址。
2. 如果仅设置了一个签名者（例如私钥、硬件钱包、密钥库），则使用该签名者。
3. 否则，将尝试使用 Foundry 默认的发送者 (`0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38`)。

现在您已经了解了智能合约脚本的功能，让我们运行它。

您应该已经将我们之前提到的变量添加到 .env 中，以便下一部分工作。

在项目运行的根目录：

```sh
# To load the variables in the .env file
source .env

# To deploy and verify our contract
forge script --chain sepolia script/NFT.s.sol:MyScript --rpc-url $SEPOLIA_RPC_URL --broadcast --verify -vvvv
```

Forge 将运行我们的脚本并为我们广播交易——这可能需要一些时间，因为 Forge 还将等待交易收据。 大约一分钟后，您应该会看到类似这样的内容：

![contract verified](https://img.learnblockchain.cn/pics/20230309091435.png)

这确认您已成功将 `NFT` 合约部署到 Sepolia 测试网，并已在 Etherscan 上对其进行了验证，所有这些都通过一个命令完成。

### 本地部署

您可以通过将端口配置为 `fork-url` 来部署到本地测试网 Anvil。

在这里，我们在帐户方面有两种选择。 我们可以在没有任何标志的情况下启动 anvil，并使用提供的私钥之一。 或者，我们可以传递一个助记符给 anvil 来使用。

#### 使用 Anvil 的默认帐户

首先，启动 Anvil：

```sh
anvil
```

使用 Anvil 提供给您的私钥更新您的 `.env `文件。

然后运行以下脚本：

```sh
forge script script/NFT.s.sol:MyScript --fork-url http://localhost:8545 --broadcast
```

#### 使用自定义助记符

将以下行添加到您的 .env 文件并使用您的助记符完成它：

```sh
MNEMONIC=
```

预计我们之前设置的 `PRIVATE_KEY` 环境变量是这个助记词中的前 10 个账户之一。

使用自定义助记符启动 Anvil：

```sh
source .env

anvil -m $MNEMONIC
```

然后运行以下脚本：

```sh
forge script script/NFT.s.sol:MyScript --fork-url http://localhost:8545 --broadcast
```

> 💡 注意：可以在 [此处](https://github.com/Perelyn-sama/solidity-scripting) 找到本教程的完整实现，要进一步阅读有关 solidity 脚本的信息，您可以查看 `forge script` [参考](../reference/forge/forge-script.md)。
