## 安装

如果在安装过程中遇到任何问题，请参阅 [常见问题解答](../faq.md) 以获取帮助。

### 预编译二进制文件

可以从 [GitHub 发布页面](https://github.com/foundry-rs/foundry/releases) 下载预编译的二进制文件。为了更方便地管理，我们推荐使用 [Foundryup](#using-foundryup)。

### 使用 Foundryup

Foundryup 是 Foundry 工具链的官方安装程序。你可以在 [这里](https://github.com/foundry-rs/foundry/blob/master/foundryup/README.md) 了解更多信息。

要安装 Foundryup，打开终端并运行以下命令：

```sh
curl -L https://foundry.paradigm.xyz | bash
```

这将安装 Foundryup。只需按照屏幕上的指示操作，`foundryup` 命令就会在你的 CLI 中可用。

运行 `foundryup` 将自动安装最新的（夜间）版本的 [预编译二进制文件](#precompiled-binaries)：`forge`、`cast`、`anvil` 和 `chisel`。有关其他选项，例如安装特定版本或提交，请运行 `foundryup --help`。

> ℹ️ **注意**  
> 如果你使用的是 Windows，你需要安装并使用 [Git BASH](https://gitforwindows.org/) 或 [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) 作为你的终端，因为 Foundryup 目前不支持 Powershell 或命令提示符（Cmd）。

### 从源代码构建

#### 先决条件

你需要 [Rust](https://rust-lang.org) 编译器和 Cargo，Rust 的包管理器。最简单的安装方法是使用 [`rustup.rs`](https://rustup.rs/)。

Foundry 通常只支持使用最新稳定版本的 Rust 构建。如果你使用的是旧版本的 Rust，可以使用 `rustup` 更新：

```sh
rustup update stable
```

对于 Windows 用户，你还需要一个最近版本的 [Visual Studio](https://visualstudio.microsoft.com/downloads/)，并安装 "Desktop Development With C++" 工作负载。

#### 构建

你可以使用 [Foundryup](#using-foundryup) 提供的各种标志：

```sh
foundryup --branch master
foundryup --path path/to/foundry
```

或者，你可以使用 Cargo 通过以下命令安装：

```sh
cargo install --git https://github.com/foundry-rs/foundry --profile release --locked forge cast chisel anvil
```

你也可以从 [Foundry 仓库](https://github.com/foundry-rs/foundry) 的本地副本手动构建：

```sh
# 克隆仓库
git clone https://github.com/foundry-rs/foundry.git
cd foundry
# 安装 Forge
cargo install --path ./crates/forge --profile release --force --locked
# 安装 Cast
cargo install --path ./crates/cast --profile release --force --locked
# 安装 Anvil
cargo install --path ./crates/anvil --profile release --force --locked
# 安装 Chisel
cargo install --path ./crates/chisel --profile release --force --locked
```

### 使用 GitHub Actions 进行 CI 安装

有关在 CI 处理流程中设置 Foundry 的说明，请参阅 [foundry-rs/foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain) GitHub Action。

### 使用 Docker 运行 Foundry

Foundry 也可以在 Docker 容器中运行。如果你没有安装 Docker，可以从 [Docker 的网站](https://docs.docker.com/get-docker/) 下载。

安装 Docker 后，你可以通过运行以下命令拉取最新的 Foundry 版本：

```sh
docker pull ghcr.io/foundry-rs/foundry:latest
```

你也可以通过从 Foundry 仓库运行以下命令在本地构建 Docker 镜像：

```sh
docker build -t foundry .
```

有关使用此镜像的示例和指南，请参阅 [Docker 教程部分](../tutorials/foundry-docker)。

> ℹ️ **注意**  
> 包括 M1 芯片在内的一些系统在本地构建 Docker 镜像时可能会遇到问题。这是一个已知问题。