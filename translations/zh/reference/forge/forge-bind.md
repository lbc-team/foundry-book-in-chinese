## forge bind

### 名称

forge-bind - 为智能合约生成 Rust 绑定。

### 简介

``forge bind`` [*options*]

### 描述

使用 [ethers-rs](https://github.com/gakonst/ethers-rs) 为智能合约生成 Rust 绑定。

绑定是由项目的 artifacts 生成的，默认情况下是 `./out/`。
如果你想为不同目录下的 artifacts 生成绑定，请传入 `--bindings-path <PATH>`。

有三个输出选项：

- 在一个 crate 中生成绑定（默认）
- 在一个模块中生成绑定，通过传递 `--module`
- 在一个单一的文件中生成绑定，通过传递 `--single-file`

默认情况下，该命令将检查现有绑定是否正确，并相应退出。
你可以通过传递以下标志来覆盖现有的绑定 `--overwrite`.

### 选项

#### Project 选项

`-b` *path*  
`--bindings-path` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;项目的根路径。默认情况下，这是当前 git 仓库的根目录，或当前工作目录。

`--crate-name` *name*  
&nbsp;&nbsp;&nbsp;&nbsp;要生成的 Rust crate 的名称，如果你正在生成一个 crate（默认）。
&nbsp;&nbsp;&nbsp;&nbsp;这应该是一个在 crates.io 里有效的 crate 名称。

&nbsp;&nbsp;&nbsp;&nbsp;默认值：foundry-contracts

`--crate-version` *semver*  
&nbsp;&nbsp;&nbsp;&nbsp;要生成的 Rust crate 的版本，如果你要生成一个 Rust crate（默认）。 
&nbsp;&nbsp;&nbsp;&nbsp;这应该是一个标准的 semver 版本字符串。

&nbsp;&nbsp;&nbsp;&nbsp;默认：0.0.1

`--module`  
&nbsp;&nbsp;&nbsp;&nbsp;以模块的形式而不是以 crate 的形式生成绑定。

`--single-file`  
&nbsp;&nbsp;&nbsp;&nbsp;将绑定生成为一个文件。

`--overwrite`  
&nbsp;&nbsp;&nbsp;&nbsp;覆盖现有的生成的绑定。默认情况下，该命令将检查绑定是否正确，然后退出。
&nbsp;&nbsp;&nbsp;&nbsp;如果传递了 `--overwrite`，它将删除并覆盖绑定。

`--root` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;项目的根路径。默认情况下，这是当前 git 仓库的根目录，或当前工作目录。

`--skip-cargo-toml`  
&nbsp;&nbsp;&nbsp;&nbsp;跳过 Cargo.toml 的一致性检查。
&nbsp;&nbsp;&nbsp;&nbsp;这允许你管理 [ethers](https://github.com/gakonst/ethers-rs) 版本而不放弃一致性检查。
&nbsp;&nbsp;&nbsp;&nbsp;一个例子是，如果你使用 ethers 的额外功能，如 `ws`，`ipc`，或 `rustls`，并提示 `ethers-providers` 版本不匹配。

`--skip-build`  
&nbsp;&nbsp;&nbsp;&nbsp;在生成绑定之前跳过运行 Forge 构建。
&nbsp;&nbsp;&nbsp;&nbsp;这允许你跳过首先执行的默认 `forge build` 步骤，而是使用已经存在的 artifacts 生成绑定。

`--select-all`  
&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，所有以 `Test` 或 `Script` 结尾的合约都被排除在外。这将明确地生成所有合约的绑定关系。与 `--select` 和 `--skip` 相冲突。

`--select` *regex+*  
&nbsp;&nbsp;&nbsp;&nbsp;只为名称符合指定过滤器的合约创建绑定。与 `--skip` 相冲突。

`--skip` *regex+*  
&nbsp;&nbsp;&nbsp;&nbsp;只为名称不符合指定过滤器的合约创建绑定。与 `--select` 相冲突。

{{#include common-options.md}}

{{#include core-build-options.md}}

### 另请参阅

[forge](./forge.md)
