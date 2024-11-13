## 对 Foundry 项目进行容器化

本教程向你展示如何使用 Foundry 的 Docker 镜像构建、测试和部署智能合约。 它改编自 [solmate nft](./solmate-nft.md) 教程中的代码。 如果你还没有完成该教程，并且是 solidity 的新手，你可能想先从它开始。 或者，如果你对 Docker 和 Solidity 有一定的了解，你可以使用自己现有的项目并进行相应的调整。 [此处](https://github.com/dmfxyz/foundry-docker-tutorial) 提供了 NFT 和 Docker 内容的完整源代码。

> 本教程仅用于说明目的，并按原样提供。 本教程未经审核或全面测试。 不应在生产环境中使用本教程中的任何代码。

### 安装和设置

运行本教程所需的唯一安装是 Docker，以及你选择的 IDE（可选）。
按照 [Docker 安装说明](/getting-started/installation.html#using-with-docker)。

  为了使以后的命令简洁明了，让我们重新标记镜像（image）：
  `docker tag ghcr.io/foundry-rs/foundry:latest foundry:latest`

在本地安装 Foundry 并不是严格要求的，但它可能有助于调试。 你可以使用 [foundryup](/getting-started/installation.html#using-foundryup) 安装它。

最后，要使用本教程的任何 `cast` 或 `forge create` 部分，你需要访问以太坊节点。 如果你没有自己的节点在运行（可能），你可以使用第三方节点服务。 我们不会在本教程中推荐特定的提供商。 开始学习节点即服务的好地方是 [Ethereum 的文章](https://ethereum.org/en/developers/docs/nodes-and-clients/nodes-as-a-service/) 主题。

**对于本教程的其余部分，假设你的以太坊节点的 RPC 端点设置如下**：`export RPC_URL=<YOUR_RPC_URL>`

### Foundry docker 镜像导览

docker 镜像可以通过两种主要方式使用：
1. 作为接口直接使用 forge 和 cast
2. 作为构建你自己的容器化测试、构建和部署工具的基础镜像

我们将涵盖两者，但让我们先看看使用 docker 与 foundry 的接口。 这也是一个很好的测试，表明你的本地安装工作正常！

我们可以针对我们的 docker 镜像运行任何 `cast` [命令](/reference/cast/)。 让我们获取最新的区块信息：

```sh
$ docker run foundry "cast block --rpc-url $RPC_URL latest"
baseFeePerGas        "0xb634241e3"
difficulty           "0x2e482bdf51572b"
extraData            "0x486976656f6e20686b"
gasLimit             "0x1c9c380"
gasUsed              "0x652993"
hash                 "0x181748772da2f968bcc91940c8523bb6218a7d57669ded06648c9a9fb6839db5"
logsBloom            "0x406010046100001198c220108002b606400029444814008210820c04012804131847150080312500300051044208430002008029880029011520380060262400001c538d00440a885a02219d49624aa110000003094500022c003600a00258009610c410323580032000849a0408a81a0a060100022505202280c61880c80020e080244400440404520d210429a0000400010089410c8408162903609c920014028a94019088681018c909980701019201808040004100000080540610a9144d050020220c10a24c01c000002005400400022420140e18100400e10254926144c43a200cc008142080854088100128844003010020c344402386a8c011819408"
miner                "0x1ad91ee08f21be3de0ba2ba6918e714da6b45836"
mixHash              "0xb920857687476c1bcb21557c5f6196762a46038924c5f82dc66300347a1cfc01"
nonce                "0x1ce6929033fbba90"
number               "0xdd3309"
parentHash           "0x39c6e1aa997d18a655c6317131589fd327ae814ef84e784f5eb1ab54b9941212"
receiptsRoot         "0x4724f3b270dcc970f141e493d8dc46aeba6fffe57688210051580ac960fe0037"
sealFields           []
sha3Uncles           "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347"
size                 "0x1d6bb"
stateRoot            "0x0d4b714990132cf0f21801e2931b78454b26aad706fc6dc16b64e04f0c14737a"
timestamp            "0x6246259b"
totalDifficulty      "0x9923da68627095fd2e7"
transactions         [...]
uncles               []
```

如果我们在一个包含一些 Solidity [源代码](https://github.com/dmfxyz/foundry-docker-tutorial) 的目录中，我们可以将该目录挂载到 docker 中，并根据需要使用 `forge`。 例如：

```sh
$ docker run -v $PWD:/app foundry "forge test --root /app --watch"
{{#include ../output/nft_tutorial/forge-test:output}}
```
你可以看到我们的代码完全在容器内编译和测试。 此外，由于我们传递了 `--watch` 选项，容器将在检测到更改时重新编译代码。

> 注意：Foundry docker 镜像基于 alpine 构建，并设计为尽可能轻量化。 因此，它目前不包括像 `git` 这样的开发资源。 如果你计划在容器内管理整个开发生命周期，你应该在 Foundry 的镜像之上构建自定义开发镜像。

### 创建一个 “构建和测试” 镜像
让我们使用 Foundry 的 docker 镜像作为使用我们自己的 Docker 镜像的基础。 我们将使用镜像来：
1. 构建我们的 solidity 代码
2. 运行我们的可靠性测试

一个简单的 `Dockerfile` 可以实现这两个目标：

```docker
# Use the latest foundry image
FROM ghcr.io/foundry-rs/foundry

# Copy our source code into the container
WORKDIR /app

# Build and test the source code
COPY . .
RUN forge build
RUN forge test
```

你可以构建此 docker 镜像并观察 forge 在容器内构建/运行测试：

```sh
$ docker build --no-cache --progress=plain 。
```

现在，如果我们的一个测试失败了怎么办？ 随意修改 `src/test/NFT.t.sol` 使其中一个测试失败。 再次尝试构建镜像。

```sh
$ docker build --no-cache --progress=plain .
<...>
#9 0.522 Failed tests:
#9 0.522 [FAIL. Reason: Ownable: caller is not the owner] testWithdrawalFailsAsNotOwner() (gas: 193917)
#9 0.522
#9 0.522 Encountered a total of 1 failing tests, 9 tests succeeded
------
error: failed to solve: executor failed running [/bin/sh -c forge test]: exit code: 1
```

我们的镜像未能建立，因为我们的测试失败了！ 这实际上是一个很好的属性，因为这意味着如果我们有一个成功构建的 Docker 镜像（因此可以使用），我们就知道镜像中的代码通过了测试。*
> *当然，docker 镜像的监管链非常重要。 Docker 层哈希对于验证非常有用。 在生产环境中，考虑[签署你的 docker 镜像](https://docs.docker.com/engine/security/trust/#:%7E:text=To%20sign%20a%20Docker%20Image,the%20local%20Docker%20trust%20repository）。

### 创建部署镜像

现在，我们将继续讨论更高级的 Dockerfile。 让我们添加一个入口点，允许我们使用构建（和测试！）的镜像来部署我们的代码。 我们可以首先针对 Rinkeby 测试网。

```docker
# Use the latest foundry image
FROM ghcr.io/foundry-rs/foundry

# Copy our source code into the container
WORKDIR /app

# Build and test the source code
COPY . .
RUN forge build
RUN forge test

# Set the entrypoint to the forge deployment command
ENTRYPOINT ["forge", "create"]
```

让我们构建镜像，这次给它命名：

```sh
$ docker build --no-cache --progress=plain -t nft-deployer .
```

以下是我们如何使用我们的 docker 镜像进行部署：

```sh
$ docker run nft-deployer --rpc-url $RPC_URL --constructor-args "ForgeNFT" "FNFT" "https://ethereum.org" --private-key $PRIVATE_KEY ./src/NFT.sol:NFT
No files changed, compilation skipped
Deployer: 0x496e09fcb240c33b8fda3b4b74d81697c03b6b3d
Deployed to: 0x23d465eaa80ad2e5cdb1a2345e4b54edd12560d3
Transaction hash: 0xf88c68c4a03a86b0e7ecb05cae8dea36f2896cd342a6af978cab11101c6224a9
```

我们刚刚在 docker 容器中完全构建、测试和部署了我们的合约！ 本教程旨在用于测试网，但你可以针对主网运行完全相同的 Docker 镜像，并确信相同的代码正在由相同的工具部署。

### 为什么这有用？

Docker 是关于可移植性、可复制性和环境不变性的。 这意味着当你在环境、网络、开发人员等之间切换时，你可以减少对意外变化的关注。以下是一些基本示例，说明为什么**我**喜欢使用 Docker 镜像进行智能合约部署：

* 减少确保系统级依赖项在部署环境之间匹配的开销（例如，你的生产运行器是否始终具有与你的开发运行器相同版本的 `forge`？）
* 增加代码在部署之前已经过测试并且没有被更改的信心（例如，如果在上面的镜像中，你的代码在部署时重新编译，这是一个主要的危险信号）。
* 缓解职责分离的痛点：拥有主网凭证的人无需确保他们拥有最新的编译器、代码库等。很容易确保某人在测试网中运行的 docker 部署映像与针对主网的相同。
* 冒着听起来像 web2 的风险，Docker 是几乎所有公共云提供商的公认标准。 它可以轻松安排需要与区块链交互的作业、任务等。


### 故障排除

如上所述，默认情况下，Foundry 镜像不包含 `git`。 这可能会导致某些命令在没有明确原因的情况下失败。 例如：

```bash
$ docker run foundry "forge init --no-git /test"
Initializing /test...
Installing ds-test in "/test/lib/ds-test", (url: https://github.com/dapphub/ds-test, tag: None)
Error:
   0: No such file or directory (os error 2)

Location:
   cli/src/cmd/forge/install.rs:107
```
在这种情况下，失败仍然是由于缺少 `git` 安装造成的。 建议的修复方法是构建现有的 Foundry 镜像并安装你需要的任何其他开发依赖项。

