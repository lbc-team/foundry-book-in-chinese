## forge clone

### 名称

forge-clone - 将链上验证的合约克隆为 Forge 项目。

### 概述

``forge clone`` [*选项*] *地址* [*根目录*]

### 描述

通过克隆链上验证合约的源代码，在目录 **根目录** 中创建一个新的 Forge 项目（默认是当前工作目录）。

默认情况下，`forge clone` 从以太坊主网（通过 Etherscan）克隆合约，但也可以通过指定链 ID 从 Foundry 支持的其他 EVM 兼容区块链克隆，例如 BNB 智能链（BSC）：`--chain <ChainID>`。

从 Etherscan 获取数据受限于速率限制。`forge clone` 需要两次 API 调用到 Etherscan，以分别收集源代码和部署信息。默认情况下，`forge clone` 在两次 Etherscan 调用之间将等待 **5** 秒，以避免速率限制错误。

通过 `--etherscan-api-key <API_KEY>` 指定 Etherscan API 密钥将增加 Etherscan API 的速率限制，并避免在 `forge clone` 中的 **5 秒** 等待时间。

与 `forge init` 一样，`forge clone` 默认会初始化一个新的 git 仓库，安装一些子模块并创建初始提交信息。

如果您不想要这种行为，请传递 `--no-git`。

### 选项

#### 克隆选项

`-c` *chain_id*  
`--chain` *chain_id*  
&nbsp;&nbsp;&nbsp;&nbsp;指定要从中克隆合约的链的名称或 EIP-155 ID。

`-e` *api_key*  
`--etherscan-api-key` *api_key*  
&nbsp;&nbsp;&nbsp;&nbsp;指定 Etherscan（或等效服务）的 API 密钥。

`--no-remappings-txt`  
&nbsp;&nbsp;&nbsp;&nbsp;将重映射放入 `foundry.toml` 配置文件中，而不是生成单独的 `remappings.txt` 文件。

#### VCS 选项

`--no-commit`  
&nbsp;&nbsp;&nbsp;&nbsp;不创建初始提交。

`--no-git`  
&nbsp;&nbsp;&nbsp;&nbsp;不创建 git 仓库。

#### 显示选项

`-q`  
`--quiet`  
&nbsp;&nbsp;&nbsp;&nbsp;不打印任何消息。

{{#include common-options.md}}

### 示例

1. 从以太坊主网克隆 UniswapV3Pool 合约：
    ```sh
    forge clone 0x8f8EF111B67C04Eb1641f5ff19EE54Cda062f163 UniswapV3Pool 
    ```

2. 克隆合约，但不创建 git 仓库：
    ```sh
    forge clone --no-git 0x8f8EF111B67C04Eb1641f5ff19EE54Cda062f163 UniswapV3Pool
    ```

3. 从 BNB 智能链（BSC）克隆合约：
    ```sh
    forge clone --chain bsc 0x7862D9B4bE2156B15d54F41ee4EDE2d5b0b455e4 UniswapV3Pool 
    ```

### 元数据

克隆的 Forge 项目在根目录中附带一个额外的 `.clone.meta` 元数据文件。  
`clone.meta` 是一个紧凑的 JSON 数据文件，包含链上合约实例的信息。  
元数据包括：
- `path`：包含在链上部署的合约声明的源文件路径。
- `targetContract`：源文件中链上合约的名称。
- `address`：链上部署的合约地址。
- `chainId`：合约部署所在链的 EIP-155 ID。
- `creationTransaction`：部署合约的交易哈希。
- `deployer`：`creationTransaction` 的发送者账户地址。
- `constructorArguments`：部署合约时使用的构造函数参数。
- `storageLayout`：链上合约的存储布局（如果相应的合约编译器版本支持导出存储布局）。

### 另见

[forge](./forge.md)