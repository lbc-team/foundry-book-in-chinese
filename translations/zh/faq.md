## 常见问题

这是一个常见问题和答案的集合。 如果你在这里没有找到你的问题, 请跳转到 [Telegram 支持频道][tg-support]
并让我们帮助你!

### 我无法从源代码构建！

确保您使用的是最新的稳定 Rust 工具链：
```sh
rustup default stable
rustup update stable
```

### 运行 `forge`/`cast` 时出现 `libusb` 错误

如果您使用发布的二进制文件，在 MacOS 上可能会看到以下错误：

```sh
dyld: Library not loaded: /usr/local/opt/libusb/lib/libusb-1.0.0.dylib
```

为了解决这个问题，您必须安装 `libusb` 库：

```sh
brew install libusb
```

### `GLIBC` 过时

如果在使用 `foundryup` 后遇到类似以下错误的情况：

```sh
forge: /lib/x86_64-linux-gnu/libc.so.6: version 'GLIBC_2.29' not found (required by forge)
```

有两种解决方法：

1. [从源代码构建](./getting-started/installation.md#building-from-source)
2. [使用 Docker](./getting-started/installation.md#using-foundry-with-docker) 

### 帮助! 我无法看到我的日志

Forge 默认不显示日志。 如果你想查看来自 Hardhat 的 `console.log` 或来自 DSTest `log_*` 样式的事件日志,
你需要使用详细级别 2（`-vv`）运行 [`forge test`][forge-test]。

如果你想查看你的合约所发出的其他事件，你需要运行并启用跟踪功能。
要做到这一点，将详细级别设置为 3（`-vvv`）以查看失败测试的跟踪，或者将详细级别设置为 4（`-vvvv`）以查看所有测试的跟踪。

### 我的测试失败了但我不知道为什么

为了更好地了解你的测试为什么会失败，可以尝试使用跟踪。 要启用跟踪，你需要将 [forge test][forge-test] 的详细级别至少提高到 3（`-vvv`），但你可以提高到 5（`-vvvvv`） 以获得更多的跟踪。

你可以在我们的 [了解 Traces][traces] 章节中学到更多关于跟踪的信息.

### 我该如何使用 `console.log`?

为了使用 Hardhat 的 `console.log`，你必须从 [此处][console-log] 复制该文件，将其添加到你的项目中。

或者，你可以使用 [Forge Std][forge-std] 附带的 `console.log`。 要使用 Forge Std 的`console.log`，
你需要导入它:

```solidity
import {console} from "forge-std/console.sol";
```

### 我该如何运行特定的测试?

如果你想只运行几个测试，你可以使用 `--match-test` 来过滤测试函数，
`--match-contract` 来过滤测试合约，以及 `--match-path` 来过滤 [`forge test`][forge-test] 中的测试文件。

### 我该如何使用特定的 Solidity 编译器?

Forge 将尝试自动检测哪个 Solidity 编译器适用于您的项目。

要使用一个特定的 Solidity 编译器，你可以在你的 [配置文件][config] 中设置 [`solc`][config-solc]，
或者将 `--use solc:<version>` 传递给支持它的 Forge 命令 (例如 [`forge build`][forge-build]
或者 [`forge test`][forge-test])。
也可以指定 solc 二进制文件路径。 要使用一个特定的本地 solc 二进制文件，你可以在配置文件中设置 `solc = "<path to solc>"`，或者通过 `--use "<path to solc>"` 指定。
solc 的版本/路径也可以通过环境变量 `FOUNDRY_SOLC=<版本/路径>` 来设置，但 cli 参数 `--use` 具有优先权。

例如，如果你有一个支持所有 0.7.x Solidity 版本的项目，但你想用 solc 0.7.0 进行编译，你可以使用`forge build --use solc:0.7.0`。

### 我该如何从实时网络分叉?

要从实时网络分叉，请将 `--fork-url <URL>` 传递给 [`forge test`][forge-test]。
你也可以使用 `--fork-block-number <BLOCK>` 从一个特定的区块分叉，这将为你的测试增加确定性，并允许 Forge 缓存该区块的链数据。

例如，要从以太坊主网的 10,000,000 区块分叉，你可以使用：`forge test --fork-url $MAINNET_RPC_URL --fork-block-number 10000000`。

### 我该如何添加自己的断言?

你可以通过创建你自己的基础测试合约，并继承你选择的测试框架来添加你自己的断言。

例如，如果你使用 DSTest，你可以创建一个这样的基础测试合约：

```solidity
contract TestBase is DSTest {
    function myCustomAssertion(uint a, uint b) {
      if (a != b) {
          emit log_string("a and b did not match");
          fail();
      }
    }
}
```

然后你将在你的测试合约中继承 `TestBase`。

```solidity
contract MyContractTest is TestBase {
    function testSomething() {
        // ...
    }
}
```

同样地，如果你使用 [Forge Std][forge-std]，你可以创建一个继承自 `Test` 的基础测试合约。

有关辅助方法和自定义断言的基础测试合约的好例子，请参见 [Solmate's `DSTestPlus`][dstestplus]。

### 我该如何离线使用 Forge?

Forge 有时会检查适合您项目的较新 Solidity 版本。要离线使用 Forge，请使用 `--offline` 标志。

### 我遇到了 Solc 错误

[solc-bin](https://binaries.soliditylang.org/) 不提供 Apple 芯片的静态构建。Foundry 依靠 [svm](https://github.com/alloy-rs/svm-rs) 来安装 Apple 芯片的本地构建。

如果目录已经存在，所有的 solc 版本都会安装在 `~/.svm/` 下。如果不存在，则使用 `$XDG_DATA_HOME/svm/`，在 Linux 上通常映射到 `$HOME/.local/share/svm/`，在 MacOS 上映射到 `$HOME/Library/Application Support/svm/`。如果遇到与 solc 相关的错误，例如 `SolcError: ...`，请删除 `~/.svm/` 目录并重试，这将触发全新安装，通常可以解决问题。

如果你使用的是 Apple 芯片，请确保 [`z3` thereom prover](https://github.com/Z3Prover/z3)已经安装。`brew install z3`.

> **注意**：原生的 Apple 芯片构建只在 `0.8.5` 以上的版本可用。如果你需要更早的版本，你必须启用 Apple 芯片的 Rosetta 来运行它们。


### Forge 在 JavaScript monorepos（`pnpm`）中失败。

像 `pnpm` 这样的管理器使用符号链接来管理 `node_modules` 文件夹。

一个常见的目录可能看起来像：

```text
├── contracts
│    ├── contracts
│    ├── foundry.toml
│    ├── lib
│    ├── node_modules
│    ├── package.json
├── node_modules
│    ├── ...
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
```

当 Foundry 工作空间在 `./contracts` 中，但 `./contracts/node_modules` 中的包被符号链接到 `./node_modules`。

当在 `./contracts/node_modules` 中运行 `forge build` 时，这可能会导致类似的错误：

```console
error[6275]: ParserError: Source "node_modules/@openzeppelin/contracts/utils/cryptography/draft-EIP712.sol" not found: File outside of allowed directories. The following are allowed: "<repo>/contracts", "<repo>/contracts/contracts", "<repo>/contracts/lib".
 --> node_modules/@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol:8:1:
  |
8 | import "../../../utils/cryptography/draft-EIP712.sol";
```

这个错误发生在 `solc` 能够解决符号链接的文件，但它们在 Foundry 工作区（`./contracts`）之外。

在 `foundry.toml` 中的 `allow_paths `中添加 `node_modules`，授予 solc 对该目录的访问权，它将能够读取该目录：

```toml
# This translates to `solc --allow-paths ../node_modules`
allow_paths = ["../node_modules"]
```

注意，该路径是相对于 Foundry 工作区的。另请参见 [solc allowed-paths](https://docs.soliditylang.org/en/latest/path-resolution.html#allowed-paths)


### 如何从源代码构建？

> **注意：**请确保你的 rust 版本是最新的：`rustup update`。当前 msrv = "1.62"

```sh
git clone https://github.com/foundry-rs/foundry
cd foundry
# install cast + forge
cargo install --path ./cli --profile local --bins --locked --force
# install anvil
cargo install --path ./anvil --profile local --locked --force
```

或通过 `cargo install --git https://github.com/foundry-rs/foundry --profile local --locked foundry-cli anvil`.

### 我收到了 `Permission denied (os error 13)`

如果你看到了如下错误

```console
Failed to create artifact parent folder "/.../MyProject/out/IsolationModeMagic.sol": Permission denied (os error 13)
```

那么很可能是文件夹权限问题。请确保 `user` 在项目根文件夹中具有写入权限。

有[报告](https://github.com/foundry-rs/foundry/issues/3268)称在 Linux 上，规范化路径可能导致奇怪的路径(`/_1/...`)。这可以通过清空整个项目文件夹并重新初始化来解决。

### 运行 `forge build` 时出现连接被拒绝

如果你无法访问 `forge build` 调用的 github URL，你会看到如下错误

```console
Error:
error sending request for url (https://raw.githubusercontent.com/roynalnaruto/solc-builds/ff4ea8a7bbde4488428de69f2c40a7fc56184f5e/macosx/aarch64/list.json): error trying to connect: tcp connect error: Connection refused (os error 61)
```

连接失败是因为你所在位置对 URL 的访问可能受到限制。为了解决这个问题，你应该设置代理。

你可以先在终端中运行 `export http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890`，然后你就可以成功运行 `forge build` 了。

[tg-support]: https://t.me/foundry_support
[forge-test]: ./reference/forge/forge-test.md
[traces]: ./forge/traces.md
[config-solc]: ./reference/config/solidity-compiler.md#solc_version
[config]: ./config/
[forge-build]: ./reference/forge/forge-build.md
[console-log]: ./reference/forge-std/console-log.md
[forge-std]: https://github.com/foundry-rs/forge-std
[dstestplus]: https://github.com/transmissions11/solmate/blob/19a4f345970ed39ee6369f343d145e0d4071c18a/src/test/utils/DSTestPlus.sol#L10
