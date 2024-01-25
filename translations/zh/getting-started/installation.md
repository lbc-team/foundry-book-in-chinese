## 安装

如果在安装过程中遇到任何问题，请查看 [常见问题解答](../faq.md) 。

### 预编译二进制文件

可以从 [GitHub 发布页面](https://github.com/foundry-rs/foundry/releases)获取预编译的二进制文件。
最好使用 [Foundryup](#using-foundryup) 来更好地管理这些文件。

### 使用 Foundryup

Foundryup 是 Foundry 工具链安装程序。您可以在 [这里](https://github.com/foundry-rs/foundry/blob/master/foundryup/README.md)找到更多信息。

打开终端并运行以下命令：

```sh
curl -L https://foundry.paradigm.xyz | bash
```

这将安装 Foundryup，然后只需按照屏幕上的说明操作，即可在 CLI 中使用 `foundryup` 命令。

仅运行 `foundryup` 将安装最新的（夜间） [预编译二进制文件](#precompiled-binaries) ：`forge`、`cast`、`anvil` 和 `chisel`。
使用 `foundryup --help` 获取更多选项，例如从特定版本或提交安装。

> ℹ️ **注意**
>
> 如果您使用 Windows，您需要安装并使用 [Git BASH](https://gitforwindows.org/) 或[WSL](https://learn.microsoft.com/en-us/windows/wsl/install)作为您的终端，因为 Foundryup 目前不支持 Powershell 或 Cmd。

### 从源代码构建

#### 先决条件

您将需要 [Rust](https://rust-lang.org) 编译器和 Cargo，Rust 包管理器。
安装两者的最简单方法是使用 [`rustup.rs`](https://rustup.rs/)。

Foundry 通常仅支持在最新的稳定 Rust 版本上构建。
如果您使用较旧的 Rust 版本，可以使用 `rustup` 进行更新：

```sh
rustup update stable
```

在 Windows 上，您还需要安装带有 “使用 C++进行桌面开发” 工作负载选项的最新版本的 [Visual Studio](https://visualstudio.microsoft.com/downloads/)。

#### 构建

您可以使用不同的 [Foundryup](#using-foundryup) 标志：

```sh
foundryup --branch master
foundryup --path path/to/foundry
```

或者，使用单个 Cargo 命令：

```sh
cargo install --git https://github.com/foundry-rs/foundry --profile local --locked forge cast chisel anvil
```

或者，通过手动从 [Foundry 存储库](https://github.com/foundry-rs/foundry)的本地副本构建：

```sh
# clone the repository
git clone https://github.com/foundry-rs/foundry.git
cd foundry
# install Forge
cargo install --path ./crates/forge --profile local --force --locked
# install Cast
cargo install --path ./crates/cast --profile local --force --locked
# install Anvil
cargo install --path ./crates/anvil --profile local --force --locked
# install Chisel
cargo install --path ./crates/chisel --profile local --force --locked
```

### 在 Github Action 中安装用于 CI

请参阅 [foundry-rs/foundry-toolchain](https://github.com/foundry-rs/foundry-toolchain) GitHub Action。

### 使用 Docker 使用 Foundry

Foundry 也可以完全在 Docker 容器中使用。如果您没有安装 Docker，可以直接从 [Docker 网站](https://docs.docker.com/get-docker/)安装。

安装后，可以通过运行以下命令下载最新版本：

```sh
docker pull ghcr.io/foundry-rs/foundry:latest
```

也可以在本地构建 docker 镜像。从 Foundry 存储库运行：

```sh
docker build -t foundry .
```

有关使用此镜像的示例和指南，请参阅 [Docker 教程部分](../tutorials/foundry-docker) 。

> ℹ️ **注意**
>
> 某些机器（包括带有 M1 芯片的机器）可能无法在本地构建 docker 镜像。 这是一个已知的问题（issue）。

