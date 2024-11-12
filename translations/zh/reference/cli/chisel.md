# chisel

快速、实用且冗长的 Solidity REPL

```bash
$ chisel --help
```

```txt
用法: chisel [选项] [命令]

命令:
  list         列出所有缓存的会话
  load         加载一个缓存的会话
  view         查看缓存会话的源代码
  clear-cache  清除缓存目录中的所有缓存 chisel 会话
  eval         简单评估一个命令而不进入 REPL
  help         打印此消息或给定子命令的帮助信息

选项:
  -h, --help
          打印帮助信息（使用 '-h' 查看摘要）

  -V, --version
          打印版本

REPL 选项:
      --prelude <PRELUDE>
          包含要导入的 Solidity 文件的目录路径，或单个 Solidity 文件的路径。
          
          这些文件将在 REPL 的顶层之前进行评估，因此充当前奏

      --no-vm
          禁用默认的 `Vm` 导入。
          
          如果 Solc 版本低于 0.6.2，则默认禁用该导入。

缓存选项:
      --force
          清除缓存和工件文件夹并重新编译

构建选项:
      --no-cache
          禁用缓存

      --eof
          使用启用 EOF 的 solc 二进制文件。通过 ir 启用并将 EVM 版本设置为
          Prague。需要安装 Docker。
          
          请注意，这是一个临时解决方案，直到 EOF 支持合并到主 solc 版本中。

      --skip <SKIP>...
          跳过构建名称包含给定过滤器的文件。
          
          `test` 和 `script` 是 `.t.sol` 和 `.s.sol` 的别名。

链接器选项:
      --libraries <LIBRARIES>
          设置预链接库
          
          [env: DAPP_LIBRARIES=]

编译器选项:
      --ignored-error-codes <ERROR_CODES>
          按错误代码忽略 solc 警告

      --deny-warnings
          警告将触发编译器错误

      --no-auto-detect
          不自动检测 `solc` 版本

      --use <SOLC_VERSION>
          指定要使用的 solc 版本，或本地 solc 的路径。
          
          有效值的格式为 `x.y.z`、`solc:x.y.z` 或
          `path/to/solc`。

      --offline
          不访问网络。
          
          缺失的 solc 版本将不会被安装。

      --via-ir
          使用 Yul 中间表示法编译处理流程

      --no-metadata
          不将任何元数据附加到字节码。
          
          这相当于将 `bytecode_hash` 设置为 `none` 和
          `cbor_metadata` 设置为 `false`。

      --silent
          启动时不打印任何内容

      --ast
          在编译器输出中以 JSON 格式包含 AST

      --evm-version <VERSION>
          目标 EVM 版本

      --optimize [<OPTIMIZE>]
          激活 Solidity 优化器
          
          [可能的值: true, false]

      --optimizer-runs <RUNS>
          运行次数大致指定每个操作码在合约生命周期内将被执行的频率。
          这意味着它是代码大小（部署成本）和代码执行成本（部署后成本）之间的权衡参数。`optimizer_runs`
          参数为 `1` 将生成短但昂贵的代码。相反，较大的 `optimizer_runs` 参数将生成更长但更节省 gas 的代码

      --extra-output <SELECTOR>...
          要包含在合约工件中的额外输出。
          
          示例键: evm.assembly, ewasm, ir, irOptimized, metadata
          
          有关完整描述，请参见
          <https://docs.soliditylang.org/en/v0.8.13/using-the-compiler.html#input-description>

      --extra-output-files <SELECTOR>...
          要写入单独文件的额外输出。
          
          有效值: metadata, ir, irOptimized, ewasm, evm.assembly

项目选项:
  -o, --out <PATH>
          合约工件文件夹的路径

      --revert-strings <REVERT>
          回退字符串配置。
          
          可能的值为 "default"、"strip"（移除）、"debug"
          （Solidity 生成的回退字符串）和 "verboseDebug"

      --build-info
          生成构建信息文件

      --build-info-path <PATH>
          构建信息文件将写入的输出路径

      --root <PATH>
          项目的根路径。
          
          默认情况下为 Git 仓库的根目录（如果在其中），或当前工作目录。

  -C, --contracts <PATH>
          合约源目录

  -R, --remappings <REMAPPINGS>
          项目的重映射

      --remappings-env <ENV>
          项目的环境重映射

      --cache-path <PATH>
          编译器缓存的路径

      --lib-paths <PATH>
          库文件夹的路径

      --hardhat
          使用 Hardhat 风格的项目布局。
          
          这与使用: `--contracts contracts --lib-paths
          node_modules` 相同。
          
          [别名: hh]

      --config-path <FILE>
          配置文件的路径

EVM 选项:
  -f, --fork-url <URL>
          通过远程端点获取状态，而不是从空状态开始。
          
          如果你想从特定区块号获取状态，请参见
          --fork-block-number。
          
          [别名: rpc-url]

      --fork-block-number <BLOCK>
          通过远程端点从特定区块号获取状态。
          
          请参见 --fork-url。

      --fork-retries <RETRIES>
          重试次数。
          
          请参见 --fork-url。

      --fork-retry-backoff <BACKOFF>
          遇到错误时的初始重试退避。
          
          请参见 --fork-url。

      --no-storage-caching
          明确禁用 RPC 缓存的使用。
          
          所有存储槽都将完全从端点读取。
          
          此标志覆盖项目的配置文件。
          
          请参见 --fork-url。

      --initial-balance <BALANCE>
          部署测试合约的初始余额

      --sender <ADDRESS>
          将执行测试/脚本的地址

      --ffi
          启用 FFI cheatcode

      --always-use-create-2-factory
          在所有情况下，包括测试和非广播脚本中使用 create 2 工厂

  -v, --verbosity...
          EVM 的详细程度。
          
          多次传递以增加详细程度（例如 -v, -vv, -vvv）。
          
          详细程度级别:
          - 2: 打印所有测试的日志
          - 3: 打印失败测试的执行跟踪
          - 4: 打印所有测试的执行跟踪，以及失败测试的设置跟踪
          - 5: 打印所有测试的执行和设置跟踪

Fork 配置:
      --compute-units-per-second <CUPS>
          设置此提供程序每秒假定可用的计算单位数量
          
          默认值: 330
          
          另请参见 --fork-url 和
          <https://docs.alchemy.com/reference/compute-units#what-are-cups-compute-units-per-second>

      --no-rpc-rate-limit
          禁用此节点提供程序的速率限制。
          
          另请参见 --fork-url 和
          <https://docs.alchemy.com/reference/compute-units#what-are-cups-compute-units-per-second>
          
          [别名: no-rate-limit]

执行环境配置:
      --gas-limit <GAS_LIMIT>
          区块 gas 限制

      --code-size-limit <CODE_SIZE>
          EIP-170: 合约代码大小限制（以字节为单位）。由于测试，增加此限制是有用的。默认值为 0x6000 (~25kb)

      --chain <CHAIN>
          链名称或 EIP-155 链 ID
          
          [别名: chain-id]

      --gas-price <GAS_PRICE>
          gas 价格

      --block-base-fee-per-gas <FEE>
          区块中的基础费用
          
          [别名: base-fee]

      --tx-origin <ADDRESS>
          交易来源

      --block-coinbase <ADDRESS>
          区块的 coinbase

      --block-timestamp <TIMESTAMP>
          区块的时间戳

      --block-number <BLOCK>
          区块号

      --block-difficulty <DIFFICULTY>
          区块难度

      --block-prevrandao <PREVRANDAO>
          区块的 prevrandao 值。注意：合并之前，此字段为 mix_hash

      --block-gas-limit <GAS_LIMIT>
          区块 gas 限制

      --memory-limit <MEMORY_LIMIT>
          每次 EVM 执行的内存限制（以字节为单位）。如果超过此限制，将抛出 `MemoryLimitOOG` 结果。
          
          默认值为 128MiB。

      --disable-block-gas-limit
          是否禁用区块 gas 限制检查
          
          [别名: no-gas-limit]

      --isolate
          是否启用调用的隔离。在隔离模式下，所有顶层调用作为单独交易在单独的 EVM 上下文中执行，从而实现更精确的 gas 计量和交易状态更改

      --alphanet
          是否启用 Alphanet 功能
```