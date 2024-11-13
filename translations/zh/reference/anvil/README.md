## anvil

### 名称

anvil - 创建一个本地测试网节点，用于部署和测试智能合约。它也可以用来分叉其他与 EVM 兼容的网络。

### 简介

`anvil` [*options*]

### 描述

创建一个本地测试网节点，用于部署和测试智能合约。它也可以用来分叉其他与 EVM 兼容的网络。

本节涵盖了关于挖矿模式、支持的传输层、支持的 RPC 方法、Anvil 标志及其用法。你可以在同一时间运行多个标志。

#### 挖矿模式
挖矿模式指的是使用 Anvil 开采区块的频率。默认情况下，一旦有交易提交，它就会自动生成一个新区块。

如果你愿意，你可以把这个设置改成定期挖矿，这意味着在用户选择的特定时间段内会产生一个新区块。如果你想采用这种挖矿方式，你可以通过添加 `--block-time <block-Time-in-seconds>` 标志来实现，就像下面的例子一样。
```sh
# 每 10 秒产生一个新的区块
anvil --block-time 10
```

还有第三种挖矿模式，叫做永不挖矿模式。在这种情况下，它禁用自动和间隔采矿，而是按需采矿。你可以通过输入以下内容来做到这一点
```sh
# 启用永不挖矿模式
anvil --no-mining
```

为了加速区块的最终确定，你可以使用 `--slots-in-an-epoch` 标志，例如设置为 `1`。这将导致高度为 `N-2` 的区块被最终确定，其中 `N` 是最新的区块。

#### 支持的传输层
支持 HTTP 和 Websocket 连接。服务器的默认监听端口为 8545，但可以通过运行以下命令来改变：

```sh
anvil --port <PORT>
```  

#### 默认的 CREATE2 部署器

Anvil 在没有分叉的情况下，包含地址为 `0x4e59b44847b379578588920ca78fbf26c0b4956c` 的[默认 CREATE2 部署器代理](https://github.com/Arachnid/deterministic-deployment-proxy) 。

这使你可以在本地测试 CREATE2 部署，而无需进行分叉。

#### 支持的 RPC 方法
##### 标准方法
标准方法是基于 [此处](https://eth.wiki/json-rpc/API) 作为参考。

* `web3_clientVersion`

* `web3_sha3`

* `eth_chainId`

* `eth_networkId`

* `eth_gasPrice`

* `eth_accounts`

* `eth_blockNumber`

* `eth_getBalance`

* `eth_getStorageAt`

* `eth_getBlockByHash`

* `eth_getBlockByNumber`

* `eth_getTransactionCount`

* `eth_getBlockTransactionCountByHash`

* `eth_getBlockTransactionCountByNumber`

* `eth_getUncleCountByBlockHash`

* `eth_getUncleCountByBlockNumber`

* `eth_getCode`

* `eth_sign`

* `eth_signTypedData_v4`

* `eth_sendTransaction`

* `eth_sendRawTransaction`

* `eth_call`

* `eth_createAccessList`

* `eth_estimateGas`

* `eth_getTransactionByHash`

* `eth_getTransactionByBlockHashAndIndex`

* `eth_getTransactionByBlockNumberAndIndex`

* `eth_getTransactionReceipt`

* `eth_getUncleByBlockHashAndIndex`

* `eth_getUncleByBlockNumberAndIndex`

* `eth_getLogs`

* `eth_newFilter`

* `eth_getFilterChanges`

* `eth_newBlockFilter`

* `eth_newPendingTransactionFilter`

* `eth_getFilterLogs`

* `eth_uninstallFilter`

* `eth_getWork`

* `eth_subscribe`

* `eth_unsubscribe`

* `eth_syncing`

* `eth_submitWork`

* `eth_submitHashrate`

* `eth_feeHistory`

* `eth_getProof`

* `debug_traceTransaction`
使用 `anvil --steps-tracing` 来获取 `structLogs`

* `debug_traceCall`
请注意，尚不支持非标准的跟踪。这意味着你不能向 `trace` 参数传递任何参数。

* `trace_transaction`

* `trace_block`

##### 自定义方法
`anvil_*` 命名空间是 `hardhat` 的别名。更多信息请参考 [Hardhat 文档](https://hardhat.org/hardhat-network/reference#hardhat-network-methods)。

`anvil_impersonateAccount`   
发送交易以外部拥有的账户或合约的身份。

`anvil_stopImpersonatingAccount`  
如果之前设置过 `anvil_impersonateAccount` 则停止冒充账户或合约。

`anvil_autoImpersonateAccount`
接受 `true` 以启用自动冒充账户，接受 `false` 以禁用它。启用后，任何交易的发送方将自动被冒充。与 `anvil_impersonateAccount` 相同。

`anvil_getAutomine`  
如果启用了自动挖矿模式，则返回 true，否则返回 false。

`anvil_mine`  
开采一系列的区块

`anvil_dropTransaction`  
将交易从池中移除

`anvil_reset`  
重置分叉到一个新的分叉状态，并可选地更新分叉配置

`anvil_setRpcUrl`  
设置后端 RPC URL

`anvil_setBalance`  
修改一个账户的余额

`anvil_setCode`  
设置一个合约的源代码

`anvil_setNonce`  
设置一个地址的 nonce

`anvil_setStorageAt`  
写入账户的单个存储槽

`anvil_setCoinbase`  
设置 coinbase 地址

`anvil_setLoggingEnabled`  
开启或关闭日志

`anvil_setMinGasPrice`  
设置节点的最小 Gas 费用

`anvil_setNextBlockBaseFeePerGas`  
设置下一个区块的基础费用

`anvil_setChainId`
设置当前 EVM 实例的链 ID

`anvil_dumpState`
返回一个代表链的完整状态的十六进制字符串。可以重新导入一个新的/重新启动的 Anvil 实例，以重新获得相同的状态。

`anvil_loadState`
当给定一个由 `anvil_dumpState` 返回的十六进制字符串时，将其内容合并到当前的链状态。将覆盖任何相同的账户/存储槽。

`anvil_nodeInfo`
检索当前运行的 Anvil 节点的配置参数。

##### 特殊方法
特殊方法来自 Ganache。你可以看一下文档 [这里](https://github.com/trufflesuite/ganache-cli-archive/blob/master/README.md)。

`evm_setAutomine`  
根据单一的布尔参数，启用或禁用在每个提交给网络的新交易中自动挖掘新区块的功能。
  
`evm_setIntervalMining`  
设置采矿行为的间隔，给定间隔时间（秒）。

`evm_snapshot`  
对区块链在当前区块的状态进行快照

`evm_revert`  
将区块链的状态恢复到之前的快照。接收一个参数，即要恢复的快照 ID。

`evm_increaseTime`  
在时间上向前跳转给定的时间量，以秒为单位

`evm_setNextBlockTimestamp`  
与 `evm_increaseTime` 类似，但在下一个区块中获取你想要的确切时间戳。

`anvil_setBlockTimestampInterval`  
类似于 `evm_increaseTime`，但设置一个区块的时间戳 `interval`。下一个区块的时间戳将被计算为 `lastBlock_timestamp + interval`。

`evm_setBlockGasLimit`  
设置接下来的区块的 Gas 限制

`anvil_removeBlockTimestampInterval`  
如果 `anvil_setBlockTimestampInterval` 存在则删除

`evm_mine`  
挖掘一个单独的区块

`anvil_enableTraces`  
打开对用户执行交易时返回的交易的调用跟踪（而不仅仅是交易哈希/交易收据）。
  
`eth_sendUnsignedTransaction`  
无论签名状态如何，都执行一项交易

对于接下来的三种方法，请务必阅读 [Geth 文档](https://geth.ethereum.org/docs/rpc/ns-txpool)。

`txpool_status`  
返回当前等待列入下一个区块的交易数量，以及安排在未来执行的交易。 

`txpool_inspect`  
返回当前等待列入下一个区块的所有交易的摘要，以及安排在未来执行的交易的摘要。 

`txpool_content`  
返回所有目前等待列入下一个区块的交易的细节，以及安排在未来执行的交易的细节。 

##### Otterscan 方法
`ots_*` 命名空间实现了[Otterscan 规范](https://github.com/otterscan/otterscan/blob/develop/docs/custom-jsonrpc.md)

`ots_getApiLevel`
被 Otterscan 用来检查是否连接到兼容的节点，并在不兼容时显示友好消息。

`ots_getInternalOperations`
返回交易内部的以太坊转账。

`ots_hasCode`
检查特定地址是否包含部署的代码。

`ots_getTransactionError`
提取交易的原始错误输出。

`ots_traceTransaction`
提取所有调用、合约创建和自毁的各种变化，并返回调用树。

`ots_getBlockDetails`
为 Otterscan 中的区块详情页面量身定制和扩展的 *eth_getBlock* 版本。

`ots_getBlockTransactions`
获取特定区块的分页交易，并删除一些冗长的字段，如日志。

`ots_searchTransactionsBefore`
分页获取特定地址在给定目标区块之前的出入交易调用。

`ots_searchTransactionsAfter`
分页获取特定地址在给定目标区块之后的出入交易调用。

`ots_getTransactionBySenderAndNonce`
根据发送者地址和 nonce 获取交易哈希。

`ots_getContractCreator`
获取创建合约的交易哈希和地址。


### 选项
#### 一般选项
`-a, --accounts <ACCOUNTS>`  
&nbsp;&nbsp;&nbsp;&nbsp; 设置账户的数量 [默认: 10]

`--auto-impersonate`
&nbsp;&nbsp;&nbsp;&nbsp; 在启动时启用 autoImpersonate

`-b, --block-time <block-time>`  
&nbsp;&nbsp;&nbsp;&nbsp; 间隔挖掘的区块时间，以秒为单位

`--balance <BALANCE>`  
&nbsp;&nbsp;&nbsp;&nbsp; 设置账户的余额 [默认: 10000]

`--derivation-path <DERIVATION_PATH>`  
&nbsp;&nbsp;&nbsp;&nbsp; 设置要派生的子密钥的派生路径 [默认: m/44'/60'/0'/0/]

`-h, --help`  
&nbsp;&nbsp;&nbsp;&nbsp; 打印帮助信息

`--hardfork <HARDFORK>`  
&nbsp;&nbsp;&nbsp;&nbsp; 选择要使用的 EVM 硬分叉
&nbsp;&nbsp;&nbsp;&nbsp; 选择硬分叉的名称, 例如 `shanghai`, `paris`, `london`, 等等...
&nbsp;&nbsp;&nbsp;&nbsp; [默认: latest]

`--init <PATH>`  
&nbsp;&nbsp;&nbsp;&nbsp; 用给定的 `genesis.json` 文件初始创世块。

`-m, --mnemonic <MNEMONIC>`  
&nbsp;&nbsp;&nbsp;&nbsp; 用于生成账户的 BIP39 助记词组

`--no-mining`  
&nbsp;&nbsp;&nbsp;&nbsp; 禁用自动和间隔开采，改为按需开采

`--order <ORDER>`  
&nbsp;&nbsp;&nbsp;&nbsp; 交易在内存池中的排序方式 [默认: fees]

`-p, --port <PORT>`  
&nbsp;&nbsp;&nbsp;&nbsp; 监听的端口号 [默认: 8545]

`--steps-tracing`  
&nbsp;&nbsp;&nbsp;&nbsp; 启用用于调试调用的步骤跟踪，返回 geth 风格的跟踪  [别名: tracing]

`--ipc [<PATH>]`  
&nbsp;&nbsp;&nbsp;&nbsp; 在给定的 `PATH` 参数或默认路径下启动一个 IPC 端点: unix: `tmp/anvil.ipc`, windows: `\\.\pipe\anvil.ipc` 

`--silent`  
&nbsp;&nbsp;&nbsp;&nbsp; 启动时不要打印任何东西

`--timestamp <TIMESTAMP>`
&nbsp;&nbsp;&nbsp;&nbsp; 设置创世块的时间戳

`-V, --version`  
&nbsp;&nbsp;&nbsp;&nbsp; 打印版本信息

`--disable-default-create2-deployer`
&nbsp;&nbsp;&nbsp;&nbsp; 在不进行分叉的模式下运行 Anvil 时禁用部署默认的 CREATE2 工厂

#### EVM 选项
`-f, --fork-url <URL>`  
&nbsp;&nbsp;&nbsp;&nbsp; 通过远程端点获取状态，而不是从一个空的状态开始

`--fork-block-number <BLOCK>`  
&nbsp;&nbsp;&nbsp;&nbsp; 通过一个远程端点从一个特定的区块号中获取状态（必须在同一个命令行中传递 `--fork-url`）。

`--fork-retry-backoff <BACKOFF>`  
&nbsp;&nbsp;&nbsp;&nbsp; 初始化遇到错误时的重试退避。

`--fork-transaction-hash <TRANSACTION>`
&nbsp;&nbsp;&nbsp;&nbsp; 通过远程端点从特定交易哈希获取状态（必须在同一命令行中传递 `--fork-url`）。

`--retries <retries>`  
&nbsp;&nbsp;&nbsp;&nbsp; 虚拟网络（超时请求）的重试次数。[默认值：5］

`--timeout <timeout>`  
&nbsp;&nbsp;&nbsp;&nbsp; 在分叉模式下向远程 JSON-RPC 服务器发送请求的超时，单位为ms。[默认值： 45000]

`--compute-units-per-second <CUPS>`  
&nbsp;&nbsp;&nbsp;&nbsp; 设置该 provider 假设每秒可用的并发数量[默认值：330] 。
&nbsp;&nbsp;&nbsp;&nbsp; 另请参阅， [Alchemy 速率限制](https://github.com/alchemyplatform/alchemy-docs/blob/master/documentation/compute-units.md#rate-limits-cups)

`--no-rate-limit`
&nbsp;&nbsp;&nbsp;&nbsp; 禁用该节点 provider 的速率限制。如果存在的话，将总是覆盖 `---compute-units-per-second`。[默认值： false]
&nbsp;&nbsp;&nbsp;&nbsp; 另请参阅， [Alchemy 速率限制](https://github.com/alchemyplatform/alchemy-docs/blob/master/documentation/compute-units.md#rate-limits-cups)

`--no-storage-caching>`  
&nbsp;&nbsp;&nbsp;&nbsp; 明确地禁止使用 RPC 缓存。所有的存储槽都完全从端点读取。这个标志覆盖了项目的配置文件（必须在同一命令行中传递 --fork-url）。


#### 执行器环境配置
`--base-fee <FEE>`  
`--block-base-fee-per-gas <FEE>`  
&nbsp;&nbsp;&nbsp;&nbsp; 区块的基础费用

`--chain-id <CHAIN_ID>`  
&nbsp;&nbsp;&nbsp;&nbsp; 链 ID [默认: 31337]

`--code-size-limit <CODE_SIZE>`  
&nbsp;&nbsp;&nbsp;&nbsp; EIP-170: 以字节为单位的合约代码大小限制。由于测试的原因，增加这一点很有用。
默认情况下，它是 0x6000 (~25kb)

`--gas-limit <GAS_LIMIT>`  
&nbsp;&nbsp;&nbsp;&nbsp; 区块的 Gas 限制

`--gas-price <GAS_PRICE>`  
&nbsp;&nbsp;&nbsp;&nbsp; Gas 价格

#### 服务器选项
`--allow-origin <allow-origin>`  
&nbsp;&nbsp;&nbsp;&nbsp; 设置 CORS 允许来源 [默认：*] 。

`--no-cors`  
&nbsp;&nbsp;&nbsp;&nbsp; 关闭 CORS

`--host <HOST>`  
&nbsp;&nbsp;&nbsp;&nbsp; 服务监听的 IP 地址

`--config-out <OUT_FILE>`  
&nbsp;&nbsp;&nbsp;&nbsp; 将 `anvil` 的输出作为 json 写入用户指定的文件中。

`--prune-history`  
&nbsp;&nbsp;&nbsp;&nbsp; 不要保留完整的链条历史

### 例子
1. 设置账户数为 15，账户余额为 300 ETH
  ```sh
  anvil --accounts 15 --balance 300
  ```

2. 选择要执行测试的地址
  ```sh
  anvil --sender 0xC8479C45EE87E0B437c09d3b8FE8ED14ccDa825E
  ```

3. 将交易在内存池中的排序方式改为先进先出
  ```sh
  anvil --order fifo
  ```

### Shell 自动补全

``anvil completions`` *shell*

为给定的 shell 生成一个 shell 自动补全脚本。

支持的 shells 有:

- bash
- elvish
- fish
- powershell
- zsh

#### 例子

1. 为 zsh 生成 shell 自动补全脚本：
    ```sh
    anvil completions zsh > $HOME/.oh-my-zsh/completions/_anvil
    ```


### 在Docker中的使用

为了在 Github Actions 中使用 [Docker容器](./tutorials/foundry-docker.md) 作为服务运行 anvil，在不能向入口命令传递参数的情况下，使用 `ANVIL_IP_ADDR` 环境变量来设置主机的 IP。`ANVIL_IP_ADDR=0.0.0.0` 等同于提供 `--host <ip>` 选项。

#### 使用 `genesis.json` 

Anvil 中的 `genesis.json` 文件与 Geth 中的作用类似，定义了网络的初始状态、共识规则以及预分配账户，以确保所有节点一致启动并保持网络完整性。包括余额、gas 限制等在内的所有数值都需要定义为十六进制。

- `chainId`：区块链的标识符，每个网络唯一。
- `nonce`：在哈希算法中使用的计数器，以确保数据完整性。
- `timestamp`：创世区块的创建时间，以 Unix 时间表示。
- `extraData`：创世区块创建者可以包含的附加数据。
- `gasLimit`：区块中可以使用的最大 gas 量。
- `difficulty`：挖掘新区块的难度程度。
- `mixHash`：证明区块有足够计算量的唯一标识符。
- `coinbase`：挖出此区块的矿工的以太坊地址。
- `stateRoot`：状态树的根，反映所有交易后的最终状态。
- `alloc`：允许为一组地址预分配具有预定义余额的以太币。
- `number`：区块编号，创世区块为 0。
- `gasUsed`：区块中使用的总 gas 量。
- `parentHash`：父区块的哈希值，创世区块为零值，因为没有父块。

模拟主网的创世文件示例可以在[此处](https://github.com/paradigmxyz/reth/blob/8f3e4a15738d8174d41f4aede5570ecead141a77/crates/primitives/res/genesis/mainnet.json)找到。

```json
{
  "chainId": "0x2323",
  "nonce": "0x42",
  "timestamp": "0x0",
  "extraData": "0x11bbe8db4e347b4e8c937c1c8370e4b5ed33adb3db69cbdb7a38e1e50b1b82fa",
  "gasLimit": "0x1388",
  "difficulty": "0x400000000",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "stateRoot": "0xd7f8974fb5ac78d9ac099b9ad5018bedc2ce0a72dad1827a1709da30580f0544",
  "alloc": {
    "000d836201318ec6899a67540690382780743280": {
      "balance": "0xad78ebc5ac6200000"
    }
  },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```