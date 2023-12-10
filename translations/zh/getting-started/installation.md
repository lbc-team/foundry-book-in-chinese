## 安装

### 在 Linux 和 macOS 上

如果您使用 Linux 或 macOS，则有两种不同的方式来安装 Foundry。

#### 使用 `foundryup` 安装最新版本

对于 Linux 和 macOS 用户来说，这是最简单的选择。

打开您的终端并输入以下命令：

```sh
curl -L https://foundry.paradigm.xyz | bash
```

这将下载`foundryup`。 然后通过运行安装 Foundry：

```sh
foundryup
```

如果一切顺利，您现在可以使用三个二进制文件：`forge`、`cast` 和 `anvil`。

如果您使用 macOS 并显示以下错误，您需要键入 `brew install libusb` 来安装库

```sh
dyld[32719]：Library not loaded：/usr/local/opt/libusb/lib/libusb-1.0.0.dylib
```

> 💡 **提示**
>
>要在安装后更新 `foundryup`，只需再次运行 `foundryup`，它将更新到最新的 Foundry 版本。 您还可以使用 `foundryup -v $VERSION` 恢复到特定版本的 Foundry。

#### 从源代码构建

要从源代码构建，您需要获取 [Rust](https://rust-lang.org) 和 Cargo。 获得两者的最简单方法是使用 `rustup`。

在 Linux 和 macOS 上，可以使用如下方式完成构建：

```sh
curl https://sh.rustup.rs -sSf | sh
```

它将下载脚本并开始安装。

### 在 Windows 上，从源代码构建

如果您使用 Windows，则需要从源代码构建以获取 Foundry。

从 [rustup.rs](https://win.rustup.rs/x86_64) 下载并运行 `rustup-init`。 它将在控制台中开始安装。

如果您遇到错误，很可能是因为您没有 VSCode安装程序，您可以[在此处下载](https://visualstudio.microsoft.com/downloads/) 并进行安装。

在此之后，运行以下命令从源代码构建 Foundry：

```sh
cargo install --git https://github.com/foundry-rs/foundry --profile local --locked forge cast chisel anvil
```

要从源代码更新，请再次运行相同的命令。

### 与 Docker 一起使用

Foundry 也可以完全在 Docker 容器中使用。 如果没有，可以直接从 [Docker 的网站](https://docs.docker.com/get-docker/) 安装 Docker。

安装后，您可以通过运行以下命令下载最新版本：

```sh
docker pull ghcr.io/foundry-rs/foundry:latest
```

也可以在本地构建 docker 镜像。 从 Foundry 存储库运行：

```sh
docker build -t foundry。
```

> ℹ️ **注意**
>
> 某些机器（包括带有 M1 芯片的机器）可能无法在本地构建 docker 镜像。 这是一个已知的问题（issue）。
