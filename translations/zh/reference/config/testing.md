## Testing

与 `forge test` 的行为有关的配置。

**Sections**

- [General](#general)
- [Fuzz](#fuzz)
- [Invariant](#invariant)


### General

##### `verbosity`

- 类型: integer
- 默认值: 0
- 环境变量: `FOUNDRY_VERBOSITY` 或 `DAPP_VERBOSITY`

在测试中使用的详细日志级别。

- **Level 2 (`-vv`)**: 显示测试中的触发的事件(日志)。
- **Level 3 (`-vvv`)**: 显示失败测试的堆栈跟踪。
- **Level 4 (`-vvvv`)**: 显示所有测试的堆栈跟踪，并显示失败测试的设置跟踪。
- **Level 5 (`-vvvvv`)**: 堆栈跟踪和设置跟踪总是被显示。

##### `ffi`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_FFI` 或 `DAPP_FFI`

是否启用 `ffi` 作弊代码。

**警告:** 启用这个作弊代码对你的项目有安全影响，因为它允许测试代码在你的计算机上执行任意程序。

##### `sender`

- 类型: string (address)
- 默认值: 0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38
- 环境变量: `FOUNDRY_SENDER` 或 `DAPP_SENDER`

测试中 `msg.sender` 的值。

##### `tx_origin`

- 类型: string (address)
- 默认值: 0x1804c8AB1F12E6bbf3894d4083f33e07309d1f38
- 环境变量: `FOUNDRY_TX_ORIGIN` 或 `DAPP_TX_ORIGIN`

测试中 `tx.origin` 的值。

##### `initial_balance`

- 类型: string (hexadecimal)
- 默认值: 0xffffffffffffffffffffffff
- 环境变量: `FOUNDRY_INITIAL_BALANCE` 或 `DAPP_INITIAL_BALANCE`

测试合约的初始余额为Wei，以16进制书写。

##### `block_number`

- 类型: integer
- 默认值: 1
- 环境变量: `FOUNDRY_BLOCK_NUMBER` 或 `DAPP_BLOCK_NUMBER`

测试中 `block.number` 的值。

##### `chain_id`

- 类型: integer
- 默认值: 31337
- 环境变量: `FOUNDRY_CHAIN_ID` 或 `DAPP_CHAIN_ID`

测试中  `chainid` 操作码的值。

##### `gas_limit`

- 类型: integer or string
- 默认值: 9223372036854775807
- 环境变量: `FOUNDRY_GAS_LIMIT` 或 `DAPP_GAS_LIMIT`

每个测试用例的 Gas 限制。

> ℹ️ **注意**
>
> 由于 Forge 的一个依赖关系的限制，如果不把数值修改为字符串，就无法修改让 Gas 限制 提高到超过默认值。
>
> 为了使用更高的Gas限制，使用一个字符串：
 ```toml
gas_limit = "18446744073709551615" # u64::MAX
```

##### `gas_price`

- 类型: integer
- 默认值: 0
- 环境变量: `FOUNDRY_GAS_PRICE` 或 `DAPP_GAS_PRICE`

测试中的 Gas 价格（单位：Wei）。

##### `block_base_fee_per_gas`

- 类型: integer
- 默认值: 0
- 环境变量: `FOUNDRY_BLOCK_BASE_FEE_PER_GAS` 或 `DAPP_BLOCK_BASE_FEE_PER_GAS`

测试中的每 Gas 基础费用（单位：Wei）。

##### `block_coinbase`

- 类型: string (address)
- 默认值: 0x0000000000000000000000000000000000000000
- 环境变量: `FOUNDRY_BLOCK_COINBASE` 或 `DAPP_BLOCK_COINBASE`

测试中 `block.coinbase` 的值。

##### `block_timestamp`

- 类型: integer
- 默认值: 1
- 环境变量: `FOUNDRY_BLOCK_TIMESTAMP` 或 `DAPP_BLOCK_TIMESTAMP`

测试中 `block.timestamp` 的值。

##### `block_difficulty`

- 类型: integer
- 默认值: 0
- 环境变量: `FOUNDRY_BLOCK_DIFFICULTY` 或 `DAPP_BLOCK_DIFFICULTY`

测试中 `block.difficulty` 的值。

##### `gas_reports`

- 类型: array of strings (contract names)
- 默认值: ["*"]
- 环境变量: `FOUNDRY_GAS_REPORTS` 或 `DAPP_GAS_REPORTS`

打印合约 Gas 报告。

##### `no_storage_caching`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_NO_STORAGE_CACHING` 或 `DAPP_NO_STORAGE_CACHING`

如果设置为 `true`，那么测试中的 RPC 端点的区块数据将不会被缓存。否则，数据将被缓存到 `$HOME/.foundry/cache/<chain id>/<block number>`。

##### `[rpc_storage_caching]`

`[rpc_storage_caching]` 区块定义哪些 RPC 端点需要被缓存。

###### `rpc_storage_caching.chains`

- 类型: string or array of strings (chain names)
- 默认值: all
- 环境变量: N/A

决定哪些链被缓存。默认情况下，所有链都被缓存。

有效值为：

- "all"
- 链名称列表，例如 `["optimism", "mainnet"]`

###### `rpc_storage_caching.endpoints`

- 类型: string 或匹配 URL 的正则数组
- 默认值: remote
- 环境变量: N/A

决定哪些RPC端点被缓存。默认情况下，只有远程端点被缓存。

有效值为：

- all
- remote (default)
- 一个正则匹配的数组，例如 `["localhost"]`

##### `eth_rpc_url`

- 类型: string
- 默认值: none
- 环境变量: `FOUNDRY_ETH_RPC_URL` 或 `DAPP_ETH_RPC_URL`

应该用于任何 rpc 调用的 rpc 服务器的 URL。

##### `etherscan_api_key`

- 类型: string
- 默认值: none
- 环境变量: `FOUNDRY_ETHERSCAN_API_KEY` 或 `DAPP_ETHERSCAN_API_KEY`

用于 RPC 调用的 etherscan API 密钥。

##### `match-test`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_MATCH_TEST` 或 `DAPP_MATCH_TEST`

只运行与正则表达式匹配的测试方法。
相当于 `forge test --match-test <TEST_PATTERN>`

##### `no-match-test`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_NO_MATCH_TEST` 或 `DAPP_NO_MATCH_TEST`

只运行与正则表达式不匹配的测试方法。
相当于 `forge test --no-match-test <TEST_PATTERN_INVERSE>`

##### `match-contract`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_MATCH_CONTRACT` 或 `DAPP_MATCH_CONTRACT`

只在与正则表达式匹配的合约里运行测试方法。
相当于 `forge test --match-contract <CONTRACT_PATTERN>`

##### `no-match-contract`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_NO_MATCH_CONTRACT` 或 `DAPP_NO_MATCH_CONTRACT`

只在与正则表达式不匹配的合约里运行测试方法。
相当于 `forge test --no-match-contract <CONTRACT_PATTERN_INVERSE>`

##### `match-path`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_MATCH_PATH` 或 `DAPP_MATCH_PATH`

只对匹配路径的文件运行测试方法。
等同于 `forge test --match-path <PATH_PATTERN>`

##### `no-match-path`

- 类型: regex
- 默认值: none
- 环境变量: `FOUNDRY_NO_MATCH_PATH` 或 `DAPP_NO_MATCH_PATH`

只在与路径不匹配的文件运行测试方法。
等同于 `forge test --no-match-path <PATH_PATTERN_INVERSE>`

##### `threads`

- 类型：整数
- 默认值：无
- 环境变量：`FOUNDRY_THREADS`

要使用的线程数。
未设置或为零表示逻辑核心的数量。

##### `show_progress`

- 类型：布尔值
- 默认值：false
- 环境变量：`FOUNDRY_SHOW_PROGRESS`

是否显示测试执行进度。

##### `block_gas_limit`

- 类型: integer
- 默认值: none
- 环境变量: `FOUNDRY_BLOCK_GAS_LIMIT` 或 `DAPP_BLOCK_GAS_LIMIT`

EVM 执行期间的 block.gaslimit 值。

##### `memory_limit`

- 类型: integer
- 默认值: 33554432
- 环境变量: `FOUNDRY_MEMORY_LIMIT` 或 `DAPP_MEMORY_LIMIT`

EVM 的内存限制（以字节为单位）。

##### `names`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_NAMES` 或 `DAPP_NAMES`

打印编译的合约名称。

##### `sizes`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_SIZES` 或 `DAPP_SIZES`

打印编译的合约大小。

##### `rpc_endpoints`

- 类型: table of RPC endpoints
- 默认值: none
- 环境变量: none

此部分位于配置文件之外，并定义了一个 RPC 端点表，其中键指定 RPC 端点的名称，值是 RPC 端点本身。

该值可以是有效的 RPC 端点或对环境变量的引用（用 `${}` 包裹）。

这些 RPC 端点可用于测试和 Solidity 脚本（参见 [`vm.rpc`](../../cheatcodes/rpc.md)）。

以下示例定义了一个名为 `optimism` 的端点和一个名为 `mainnet` 的端点，它引用了一个环境变量`RPC_MAINNET`:

```toml
[rpc_endpoints]
optimism = "https://optimism.alchemyapi.io/v2/..."
mainnet = "${RPC_MAINNET}"
```

##### `prompt_timeout`

- 类型：整数
- 默认值：120
- 环境变量：`FOUNDRY_PROMPT_TIMEOUT`

在 `vm.prompt` 由于超时而回退之前等待的秒数。

字典的权重。更高的字典权重将使模糊输入偏向“有趣”的值，例如边界值如 `type(uint256).max` 或来自你环境的合约地址。

### Fuzz

`[fuzz]` 部分的配置值。

##### `runs`

- 类型: integer
- 默认值: 256
- 环境变量: `FOUNDRY_FUZZ_RUNS` 或 `DAPP_FUZZ_RUNS`

每个模糊测试用例要执行的模糊运行量。更高的值会以测试速度为代价提高结果的可信度。

##### `max_test_rejects`

- 类型: integer
- 默认值: 65536
- 环境变量: `FOUNDRY_FUZZ_MAX_TEST_REJECTS`

在整个测试中止之前可以拒绝的组合输入的最大数量。
“全局” 过滤器适用于整个测试用例。如果测试用例被拒绝，则整个测试用例将重新生成

##### `seed`

- 类型: string (hexadecimal)
- 默认值: none
- 环境变量: `FOUNDRY_FUZZ_SEED`

模糊 RNG 算法的可选种子。

##### `dictionary_weight`

- 类型: integer (between 0 and 100)
- 默认值: 40
- 环境变量: `FOUNDRY_FUZZ_DICTIONARY_WEIGHT`

字典的权重。更高的字典权重将使模糊输入偏向“有趣”的值，例如边界值如 `type(uint256).max` 或来自你环境的合约地址。

##### `include_storage`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_FUZZ_INCLUDE_STORAGE`

标志是否包含存储中的值。

##### `include_push_bytes`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_FUZZ_INCLUDE_PUSH_BYTES`

标志是否包含推送字节值。

##### `show_logs`

- 类型：布尔值
- 默认值：false
- 环境变量：`FOUNDRY_FUZZ_SHOW_LOGS`

该标志指示是否在模糊测试中显示控制台日志。请注意，为了启用显示控制台日志，你需要设置 `show_logs = true`，然后使用 `forge test -vv` 或设置 `verbosity >= 2`。

### Invariant

`[invariant]` 部分的配置值。

> ℹ️ **注意**
>
> `[invariant]` 部分的配置具有回退逻辑
> 对于常见的配置条目（`runs`、`seed`、`dictionary_weight` 等）。
>
> * 如果在任一部分中都没有设置条目，则将使用默认值。
> * 如果条目在 `[fuzz]` 部分中设置，但未在 `[invariant]` 中设置
>   条目, 这些值将自动设置为指定的值
>   在 `[fuzz]` 条目.
> * 对于 `default` 以外的任何配置文件：
>     * 如果在 `[invariant]` 中设置公共条目 (类似
>        `[profile.default.invariant]`) 条目, 从
>       `[invariant]` 条目的值会被使用。
>     * 如果公共条目未在 `[invariant]` 部分中设置，
>       但是在 `[fuzz]` 条目中设置 (类似 `[profile.default.fuzz]`) ,
>       然后将使用 `[fuzz]` 部分的值。
>     * 如果不是上述任何一种情况，
>       则将使用默认值。

##### `runs`

- 类型: integer
- 默认值: 256
- 环境变量: `FOUNDRY_INVARIANT_RUNS`

必须为每个不变测试组执行的运行次数。 另见 [fuzz.runs](#runs)

##### `depth`

- 类型: integer
- 默认值: 500
- 环境变量: `FOUNDRY_INVARIANT_DEPTH`

在一次运行中为尝试破坏不变量而执行的调用次数。

##### `fail_on_revert`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_INVARIANT_FAIL_ON_REVERT`

如果发生 revert，不变的模糊测试将失败。

##### `call_override`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_INVARIANT_CALL_OVERRIDE`

允许在运行不变测试时覆盖不安全的外部调用。 例如： 重入检查。

##### `dictionary_weight`

- 类型: integer (between 0 and 100)
- 默认值: 80
- 环境变量: `FOUNDRY_INVARIANT_DICTIONARY_WEIGHT`

字典的权重。另见 [fuzz.dictionary_weight](#dictionary_weight)

##### `include_storage`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_FUZZ_INCLUDE_STORAGE`

指示是否包括存储中的值的标志。另见 [fuzz.include_storage](#include_storage)

##### `include_push_bytes`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_FUZZ_INCLUDE_PUSH_BYTES`

指示是否包含推送字节值的标志。 另见 [fuzz.include_push_bytes](#include_push_bytes)

##### `shrink_run_limit`

- 类型：整数
- 默认值：5000
- 环境变量：`FOUNDRY_INVARIANT_SHRINK_RUN_LIMIT`

缩小失败序列的最大尝试次数。如果设置为 0，则禁用缩小过程。