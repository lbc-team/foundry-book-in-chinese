# anvil

一个快速的本地以太坊开发节点

```bash
$ anvil --help
```

```txt
用法: anvil [选项] [命令]

命令:
  completions        生成 shell 完成脚本 [别名: com]
  generate-fig-spec  生成 Fig 自动补全规范 [别名: fig]
  help               打印此消息或给定子命令的帮助

选项:
  -a, --accounts <NUM>
          生成和配置的开发账户数量
          
          [默认: 10]

  -b, --block-time <SECONDS>
          间隔挖矿的区块时间（秒）
          
          [别名: blockTime]

      --balance <NUM>
          每个开发账户的以太余额
          
          [默认: 10000]

      --config-out <OUT_FILE>
          将 `anvil` 的输出以 json 格式写入用户指定的文件

      --derivation-path <DERIVATION_PATH>
          设置要派生的子密钥的派生路径。
          
          [默认: m/44'/60'/0'/0/]

      --dump-state <PATH>
          在退出时将链的状态和区块环境转储到给定文件。
          
          如果值是一个目录，状态将写入
          `<VALUE>/state.json`。

  -h, --help
          打印帮助（使用 '-h' 查看摘要）

      --hardfork <HARDFORK>
          要使用的 EVM 硬分叉。
          
          通过名称选择硬分叉，例如 `shanghai`、`paris`、`london`，
          等等... [默认: latest]

      --init <PATH>
          使用给定的 `genesis.json` 文件初始化创世区块

      --ipc [<PATH>]
          在给定路径或默认路径 `/tmp/anvil.ipc` 启动 ipc 服务器
          
          [别名: ipcpath]

      --load-state <PATH>
          从先前保存的状态快照初始化链

  -m, --mnemonic <MNEMONIC>
          用于生成账户的 BIP39 助记词短语。如果使用了
          `mnemonic_random` 或 `mnemonic_seed`，则不能使用

      --max-persisted-states <MAX_PERSISTED_STATES>
          磁盘上要持久化的最大状态数量。
          
          请注意，`prune_history` 将覆盖 `max_persisted_states` 为 0。

      --mixed-mining
          [别名: mixed-mining]

      --mnemonic-random [<MNEMONIC_RANDOM>]
          自动生成 BIP39 助记词短语，并从中派生账户。
          不能与其他 `mnemonic` 选项一起使用。你可以指定
          助记词中要包含的单词数量。 [默认: 12]

      --mnemonic-seed-unsafe <MNEMONIC_SEED>
          从给定种子生成 BIP39 助记词短语。不能与其他
          `mnemonic` 选项一起使用。
          
          小心: 这不是安全的，只应在测试中使用。切勿在生产中使用生成的私钥。

      --no-mining
          禁用自动和间隔挖矿，而是按需挖矿
          
          [别名: no-mine]

      --order <ORDER>
          交易在内存池中的排序方式
          
          [默认: fees]

  -p, --port <NUM>
          监听的端口号
          
          [默认: 8545]

      --preserve-historical-states
          在转储状态时保留历史状态快照。
          
          这将保存链在特定区块哈希时的内存状态。
          
          当使用 `--load-state` / `--state` 时，这些历史状态将被加载到内存中，并有助于超出转储状态的区块的 RPC 调用。

      --prune-history [<PRUNE_HISTORY>]
          不保留完整的链历史。如果指定了数字参数，最多保留此数量的状态在内存中。
          
          如果启用，则不会在磁盘上持久化任何状态，因此
          `max_persisted_states` 将为 0。

  -s, --state-interval <SECONDS>
          状态和区块环境转储到磁盘的间隔（秒）。
          
          参见 --state 和 --dump-state

      --silent
          启动时不打印任何内容，并且不打印日志

      --slots-in-an-epoch <SLOTS_IN_AN_EPOCH>
          每个纪元中的插槽数量
          
          [默认: 32]

      --state <PATH>
          这是 --load-state 和 --dump-state 的别名。
          
          如果文件存在，它将使用存储在文件中的状态和区块环境初始化链，并在退出时转储链的状态。

      --timestamp <NUM>
          创世区块的时间戳

      --transaction-block-keeper <TRANSACTION_BLOCK_KEEPER>
          内存中要保留的交易区块数量

  -V, --version
          打印版本

服务器选项:
      --allow-origin <ALLOW_ORIGIN>
          cors `allow_origin` 头部
          
          [默认: *]

      --host <IP_ADDR>
          服务器将监听的主机
          
          [环境: ANVIL_IP_ADDR=]
          [默认: 127.0.0.1]

      --no-cors
          禁用 CORS

      --no-request-size-limit
          禁用默认的请求体大小限制。撰写时默认限制为 2MB

分叉配置:
      --compute-units-per-second <CUPS>
          设置此提供者每秒假定可用的计算单位数量
          
          默认值: 330
          
          另请参见 --fork-url 和
          <https://docs.alchemy.com/reference/compute-units#what-are-cups-compute-units-per-second>

  -f, --fork-url <URL>
          从远程端点获取状态，而不是从空状态开始。
          
          如果你想从特定区块号获取状态，请添加区块号，例如 `http://localhost:8545@1400000` 或使用
          `--fork-block-number` 参数。
          
          [别名: rpc-url]

      --fork-block-number <BLOCK>
          从远程端点获取特定区块号的状态。
          
          参见 --fork-url。

      --fork-chain-id <CHAIN>
          指定链 ID，以跳过从远程端点获取。启用离线启动模式。
          
          你仍然必须传递 `--fork-url` 和 `--fork-block-number`，并且已经在磁盘上缓存了所需的状态，任何本地缺失的内容将从远程获取。

      --fork-header <HEADERS>
          用于 rpc 客户端的头部，例如 "User-Agent: test-agent"
          
          参见 --fork-url。

      --fork-retry-backoff <BACKOFF>
          遇到错误时的初始重试退避。
          
          参见 --fork-url。

      --fork-transaction-hash <TRANSACTION>
          从远程端点获取特定交易哈希的状态。
          
          参见 --fork-url。

      --no-rate-limit
          禁用此节点提供者的速率限制。
          
          默认值: false
          
          另请参见 --fork-url 和
          <https://docs.alchemy.com/reference/compute-units#what-are-cups-compute-units-per-second>
          
          [别名: no-rpc-rate-limit]

      --no-storage-caching
          明确禁用 RPC 缓存的使用。
          
          所有存储插槽都完全从端点读取。
          
          此标志会覆盖项目的配置文件。
          
          参见 --fork-url。

      --retries <retries>
          对于虚假网络（超时请求）的重试请求数量
          
          默认值 5

      --timeout <timeout>
          在分叉模式下发送到远程 JSON-RPC 服务器的请求超时（毫秒）。
          
          默认值 45000

环境配置:
      --block-base-fee-per-gas <FEE>
          区块中的基本费用
          
          [别名: base-fee]

      --chain-id <CHAIN_ID>
          链 ID

      --code-size-limit <CODE_SIZE>
          EIP-170: 合约代码大小限制（字节）。由于测试，增加此限制是有用的。要完全禁用，请使用
          `--disable-code-size-limit`。默认值为 0x6000 (~25kb)

      --disable-block-gas-limit
          禁用 `call.gas_limit <= block.gas_limit` 约束

      --disable-code-size-limit
          禁用 EIP-170: 合约代码大小限制

      --disable-min-priority-fee
          禁用强制最低建议优先费用
          
          [别名: no-priority-fee]

      --gas-limit <GAS_LIMIT>
          区块 gas 限制

      --gas-price <GAS_PRICE>
          gas 价格

EVM 选项:
      --alphanet
          启用 Alphanet 功能
          
          [别名: odyssey]

      --auto-impersonate
          启动时启用 autoImpersonate
          
          [别名: auto-impersonate]

      --disable-console-log
          禁用将 `console.log` 调用打印到 stdout
          
          [别名: no-console-log]

      --disable-default-create2-deployer
          禁用默认的 create2 部署者
          
          [别名: no-create2]

      --memory-limit <MEMORY_LIMIT>
          每次 EVM 执行的内存限制（字节）

      --optimism
          运行 Optimism 链
          
          [别名: optimism]

      --steps-tracing
          启用用于调试调用的步骤跟踪，返回 geth 风格的跟踪
          
          [别名: tracing]
```