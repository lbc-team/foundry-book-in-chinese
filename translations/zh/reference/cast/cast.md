## cast

### 名称

cast - 从你的命令行中舒适地执行 Ethereum RPC 调用。

### 简介

`cast` [*options*] *command* [*args*]
`cast` [*options*] `--version`
`cast` [*options*] `--help`

### 描述

这个程序是一套与以太坊互动并进行转换的工具。

### 命令

#### General 命令

[cast help](./cast-help.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取 Cast 命令的帮助。

[cast completions](./cast-completions.md)
&nbsp;&nbsp;&nbsp;&nbsp;生成 shell 自动补全脚本。

#### Chain 命令

[cast chain-id](./cast-chain-id.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取 Ethereum 的链 ID。

[cast chain](./cast-chain.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取当前链的名称。

[cast client](./cast-client.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取当前客户端的版本。

#### Transaction 命令

[cast publish](./cast-publish.md)
&nbsp;&nbsp;&nbsp;&nbsp;向网络发布一个原始交易。

[cast receipt](./cast-receipt.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个交易的交易收据。

[cast send](./cast-send.md)
&nbsp;&nbsp;&nbsp;&nbsp;签署并发布一项交易。

[cast call](./cast-call.md)
&nbsp;&nbsp;&nbsp;&nbsp;在不发布交易的情况下对一个账户进行调用。

[cast rpc](./cast-rpc.md)
&nbsp;&nbsp;&nbsp;&nbsp;执行一个原始的 JSON-RPC 请求 [aliases: rp]

[cast tx](./cast-tx.md)
&nbsp;&nbsp;&nbsp;&nbsp;获得有关交易的信息。

[cast run](./cast-run.md)
&nbsp;&nbsp;&nbsp;&nbsp;在本地环境中运行一个已发布的交易，并打印出跟踪。

[cast estimate](./cast-estimate.md)
&nbsp;&nbsp;&nbsp;&nbsp;估算交易的 Gas 成本。

[cast access-list](./cast-access-list.md)
&nbsp;&nbsp;&nbsp;&nbsp;为一个交易创建一个访问列表。

[cast logs](./cast-logs.md)
&nbsp;&nbsp;&nbsp;&nbsp;按签名或主题获取日志

#### Block 命令

[cast find-block](./cast-find-block.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取与提供的时间戳最接近的区块编号。

[cast gas-price](./cast-gas-price.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取当前 Gas 价格。

[cast block-number](./cast-block-number.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取最新的区块号。

[cast basefee](./cast-basefee.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个区块的基础费用。

[cast block](./cast-block.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个区块的信息。

[cast age](./cast-age.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个区块的时间戳。

#### Account 命令

[cast balance](./cast-balance.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个账户的余额，单位为 Wei。

[cast storage](./cast-storage.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个合约的存储槽的原始值。

[cast proof](./cast-proof.md)
&nbsp;&nbsp;&nbsp;&nbsp;为一个给定的存储槽生成一个存储证明。

[cast nonce](./cast-nonce.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个账户的 nonce。

[cast code](./cast-code.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个合约的字节码。

[cast codesize](./cast-codesize.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取合约的运行时字节码大小。

#### ENS 命令

[cast lookup-address](./cast-lookup-address.md)
&nbsp;&nbsp;&nbsp;&nbsp;进行 ENS 反向查询。

[cast resolve-name](./cast-resolve-name.md)
&nbsp;&nbsp;&nbsp;&nbsp;进行 ENS 查询。

[cast namehash](./cast-namehash.md)
&nbsp;&nbsp;&nbsp;&nbsp;计算一个名字的 ENS namehash。

#### Etherscan 命令

[cast etherscan-source](./cast-etherscan-source.md)
&nbsp;&nbsp;&nbsp;&nbsp;从 Etherscan 获取合约的源代码。

#### ABI 命令

[cast abi-decode](./cast-abi-decode.md)
&nbsp;&nbsp;&nbsp;&nbsp;解码 ABI 编码的输入或输出数据。

[cast abi-encode](./cast-abi-encode.md)
&nbsp;&nbsp;&nbsp;&nbsp;对给定的函数参数进行 ABI 编码，不包括选择器。

[cast 4byte](./cast-4byte.md)
&nbsp;&nbsp;&nbsp;&nbsp;从 <https://sig.eth.samczsun.com> 中获取指定选择器的函数签名。

[cast 4byte-decode](./cast-4byte-decode.md)
&nbsp;&nbsp;&nbsp;&nbsp;使用 <https://sig.eth.samczsun.com> 对 ABI 编码的 calldata 进行解码。

[cast 4byte-event](./cast-4byte-event.md)
&nbsp;&nbsp;&nbsp;&nbsp;从 <https://sig.eth.samczsun.com> 中获取 topic 0 的事件签名。

[cast calldata](./cast-calldata.md)
&nbsp;&nbsp;&nbsp;&nbsp;ABI 编码一个带参数的函数。

[cast calldata-decode](./cast-calldata-decode.md)
&nbsp;&nbsp;&nbsp;&nbsp;解码 ABI 编码的输入数据。

[cast pretty-calldata](./cast-pretty-calldata.md)
&nbsp;&nbsp;&nbsp;&nbsp;漂亮地打印 Calldata。

[cast selectors](./cast-selectors.md)
&nbsp;&nbsp;&nbsp;&nbsp;从字节码中提取函数选择器和参数

[cast upload-signature](./cast-upload-signature.md)
&nbsp;&nbsp;&nbsp;&nbsp;将指定的签名上传到 https://sig.eth.samczsun.com.

#### Conversion 命令

[cast format-bytes32-string](./cast-format-bytes32-string.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个字符串转换成 bytes32 编码。

[cast from-bin](./cast-from-bin.md)
&nbsp;&nbsp;&nbsp;&nbsp;将二进制数据转换为十六进制数据。

[cast from-fix](./cast-from-fix.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个定点数转换成一个整数。

[cast from-utf8](./cast-from-utf8.md)
&nbsp;&nbsp;&nbsp;&nbsp;将 UTF8 文本转换为十六进制。

[cast from-wei](./cast-from-wei.md)
&nbsp;&nbsp;&nbsp;&nbsp;将 wei 转换为 ETH 数量

[cast parse-bytes32-address](./cast-parse-bytes32-address.md)
&nbsp;&nbsp;&nbsp;&nbsp;从 bytes32 编码中解析出校验和地址。

[cast parse-bytes32-string](./cast-parse-bytes32-string.md)
&nbsp;&nbsp;&nbsp;&nbsp;从 bytes32 编码中解析出一个字符串。

[cast to-ascii](./cast-to-ascii.md)
&nbsp;&nbsp;&nbsp;&nbsp;将十六进制数据转换为ASCII字符串。

[cast to-base](./cast-to-base.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个进制底数转换为其它底数。

[cast to-bytes32](./cast-to-bytes32.md)
&nbsp;&nbsp;&nbsp;&nbsp;右移十六进制数据至 32 字节。

[cast to-dec](./cast-to-dec.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个基数的数字转换为十进制

[cast to-fixed-point](./cast-to-fixed-point.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个整数转换成一个定点数。

[cast to-hex](./cast-to-hex.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个基数的数字转换为另一个基数

[cast to-hexdata](./cast-to-hexdata.md)
&nbsp;&nbsp;&nbsp;&nbsp;将输入规范化为小写，0x- 前缀的十六进制。

[cast to-int256](./cast-to-int256.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个数字转换为十六进制编码的 int256。

[cast to-rlp](./cast-to-rlp.md)
&nbsp;&nbsp;&nbsp;&nbsp;对十六进制数据或十六进制数据数组进行 RLP 编码

[cast to-uint256](./cast-to-uint256.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个数字转换成十六进制编码的 uint256。

[cast to-unit](./cast-to-unit.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个 eth 单位转换为另一个单位。 (ether, gwei, wei).

[cast to-wei](./cast-to-wei.md)
&nbsp;&nbsp;&nbsp;&nbsp;将 eth 金额转换为 wei 单位。

[cast shl](./cast-shl.md)
&nbsp;&nbsp;&nbsp;&nbsp;进行左移操作。

[cast shr](./cast-shr.md)
&nbsp;&nbsp;&nbsp;&nbsp;进行右移操作。

#### Utility Commands

[cast address-zero](./cast-address-zero.md)
&nbsp;&nbsp;&nbsp;&nbsp;打印零地址.

[cast sig](./cast-sig.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取一个函数的选择器。

[cast sig-event](./cast-sig-event.md)
&nbsp;&nbsp;&nbsp;&nbsp;从事件字符串生成事件签名。

[cast keccak](./cast-keccak.md)
&nbsp;&nbsp;&nbsp;&nbsp;使用 keccak-256 对任意数据进行哈希。

[cast compute-address](./cast-compute-address.md)
&nbsp;&nbsp;&nbsp;&nbsp;从给定的 nonce 和部署者地址计算合约地址。

[cast create2](./cast-create2.md)
&nbsp;&nbsp;&nbsp;&nbsp; 使用 CREATE2 生成确定性合约地址。

[cast interface](./cast-interface.md)
&nbsp;&nbsp;&nbsp;&nbsp;从一个给定的 ABI 生成一个 Solidity 接口。

[cast index](./cast-index.md)
&nbsp;&nbsp;&nbsp;&nbsp;计算集合中条目的存储插槽位置。

[cast concat-hex](./cast-concat-hex.md)
&nbsp;&nbsp;&nbsp;&nbsp;串接十六进制字符串。

[cast max-int](./cast-max-int.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取 int256 最大值。

[cast min-int](./cast-min-int.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取 int256 最小值。

[cast max-uint](./cast-max-uint.md)
&nbsp;&nbsp;&nbsp;&nbsp;获取 uint256 最大值。

[cast to-check-sum-address](./cast-to-check-sum-address.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个地址转换为校验过的格式 (EIP-55).

#### Wallet Commands

[cast wallet](./cast-wallet.md)
&nbsp;&nbsp;&nbsp;&nbsp;钱包管理实用工具。

[cast wallet new](./cast-wallet-new.md)
&nbsp;&nbsp;&nbsp;&nbsp;创建一个新的随机密钥对。

[cast wallet address](./cast-wallet-address.md)
&nbsp;&nbsp;&nbsp;&nbsp;将一个私钥转换为一个地址。

[cast wallet sign](./cast-wallet-sign.md)
&nbsp;&nbsp;&nbsp;&nbsp;签署消息。

[cast wallet vanity](./cast-wallet-vanity.md)
&nbsp;&nbsp;&nbsp;&nbsp;生成一个虚构的地址。

[cast wallet verify](./cast-wallet-verify.md)
&nbsp;&nbsp;&nbsp;&nbsp;验证一个信息的签名。

### 可选

#### Special 选项

`-V`
`--version`
&nbsp;&nbsp;&nbsp;&nbsp;打印版本信息并退出。

{{#include common-options.md}}

### 例子

1. 在一个合约上调用一个函数。

    ```sh
    cast call 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 \
      "balanceOf(address)(uint256)" 0x...
    ```

2. 解码原始的 calldata。

    ```sh
    cast calldata-decode "transfer(address,uint256)" \
      0xa9059cbb000000000000000000000000e78388b4ce79068e89bf8aa7f218ef6b9ab0e9d0000000000000000000000000000000000000000000000000008a8e4b1a3d8000
    ```

3. 编码 calldata:
    ```sh
    cast calldata "someFunc(address,uint256)" 0x... 1
    ```

### BUGS

issues 请见 <https://github.com/foundry-rs/foundry/issues> 。
