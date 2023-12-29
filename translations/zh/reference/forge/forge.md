## forge

### 名称

forge - 构建、测试、模糊、调试和部署 Solidity 合约。

### 简介

`forge` [*options*] *command* [*args*]  
`forge` [*options*] `--version`  
`forge` [*options*] `--help`

### 描述

该程序是一套用于构建、测试、模糊、调试和部署 Solidity 智能合约的工具。

### 命令

#### 一般命令

[forge help](./forge-help.md)  
&nbsp;&nbsp;&nbsp;&nbsp;显示关于 Forge 的帮助信息。

[forge completions](./forge-completions.md)  
&nbsp;&nbsp;&nbsp;&nbsp;为 Forge 生成 shell 的自动补全。

#### 项目命令

[forge init](./forge-init.md)  
&nbsp;&nbsp;&nbsp;&nbsp;创建一个新的 Forge 项目。

[forge install](./forge-install.md)  
&nbsp;&nbsp;&nbsp;&nbsp;安装一个或多个依赖。

[forge update](./forge-update.md)  
&nbsp;&nbsp;&nbsp;&nbsp;更新一个或多个依赖。

[forge remove](./forge-remove.md)  
&nbsp;&nbsp;&nbsp;&nbsp;移除一个或多个依赖。

[forge config](./forge-config.md)  
&nbsp;&nbsp;&nbsp;&nbsp;显示当前配置。

[forge remappings](./forge-remappings.md)  
&nbsp;&nbsp;&nbsp;&nbsp;获取这个项目的自动推断的重映射。

[forge tree](./forge-tree.md)  
&nbsp;&nbsp;&nbsp;&nbsp;显示项目的树状可视化依赖关系图。

[forge geiger](./forge-geiger.md)
&nbsp;&nbsp;&nbsp;&nbsp;检测在 Foundry 项目及其依赖中使用的不安全作弊码。

#### 构建命令

[forge build](./forge-build.md)  
&nbsp;&nbsp;&nbsp;&nbsp;构建项目的智能合约。

[forge clean](./forge-clean.md)  
&nbsp;&nbsp;&nbsp;&nbsp;移除构建的 artifacts 和 cache 目录。

[forge inspect](./forge-inspect.md)  
&nbsp;&nbsp;&nbsp;&nbsp;获取关于智能合约的专门信息。

#### 测试命令

[forge test](./forge-test.md)  
&nbsp;&nbsp;&nbsp;&nbsp;运行项目的测试。

[forge snapshot](./forge-snapshot.md)  
&nbsp;&nbsp;&nbsp;&nbsp;创建每个测试的 Gas 使用快照。

[forge coverage](./forge-coverage.md)  
&nbsp;&nbsp;&nbsp;&nbsp;生成覆盖率报告

#### 部署命令

[forge create](./forge-create.md)  
&nbsp;&nbsp;&nbsp;&nbsp;部署一个智能合约

[forge verify-contract](./forge-verify-contract.md)  
&nbsp;&nbsp;&nbsp;&nbsp;在 Etherscan 上验证智能合约。

[forge verify-check](./forge-verify-check.md)  
&nbsp;&nbsp;&nbsp;&nbsp;在 Etherscan 上检查验证状态。

[forge flatten](./forge-flatten.md)  
&nbsp;&nbsp;&nbsp;&nbsp;将一个源文件及其所有的导入文件扁平化到一个文件中。

#### 实用命令

[forge debug](./forge-debug.md)  
&nbsp;&nbsp;&nbsp;&nbsp;将单个智能合约作为脚本进行调试。

[forge bind](./forge-bind.md)  
&nbsp;&nbsp;&nbsp;&nbsp;为智能合约生成 Rust bindings。

[forge cache](./forge-cache.md)  
&nbsp;&nbsp;&nbsp;&nbsp;管理 Foundry 缓存。

[forge cache clean](./forge-cache-clean.md)  
&nbsp;&nbsp;&nbsp;&nbsp;从 ``~/.foundry`` 清除缓存数据。

[forge cache ls](./forge-cache-ls.md)  
&nbsp;&nbsp;&nbsp;&nbsp;从 ``~/.foundry`` 显示缓存数据。

[forge script](./forge-script.md)  
&nbsp;&nbsp;&nbsp;&nbsp;以脚本形式运行智能合约，建立可在链上发送的交易。

[forge upload-selectors](./forge-upload-selectors.md)  
&nbsp;&nbsp;&nbsp;&nbsp;将给定合约的 ABI 上传至 https://sig.eth.samczsun.com 函数选择器数据库。

[forge doc](./forge-doc.md)  
&nbsp;&nbsp;&nbsp;&nbsp;为 Solidity 源文件生成文档。

### 选项

#### 特别选项

`-V`  
`--version`  
&nbsp;&nbsp;&nbsp;&nbsp;打印版本信息并退出。

{{#include common-options.md}}

### 文件

`~/.foundry/`  
&nbsp;&nbsp;&nbsp;&nbsp;Foundry 的 "home" 目录的默认位置，它存储各种文件。

`~/.foundry/bin/`  
&nbsp;&nbsp;&nbsp;&nbsp;使用 `foundryup` 安装的二进制文件将位于这里。

`~/.foundry/cache/`  
&nbsp;&nbsp;&nbsp;&nbsp;Forge 的缓存目录，它在那里存储缓存的块数据和更多。

`~/.foundry/foundry.toml`  
&nbsp;&nbsp;&nbsp;&nbsp;全局 [Foundry 配置](../config/overview.md).

`~/.svm`  
&nbsp;&nbsp;&nbsp;&nbsp;Forge 管理的 solc 二进制文件的位置。

### 例子

1. 创建一个新的 Forge 项目：
    ```sh
    forge init hello_foundry
    ```

2. 构建一个项目：
    ```sh
    forge build
    ```

3. 运行一个项目的测试：
    ```sh
    forge test
    ```

### BUGS

Issues 请看 <https://github.com/foundry-rs/foundry/issues> 
