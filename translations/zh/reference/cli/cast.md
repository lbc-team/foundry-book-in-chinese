# cast

从命令行舒适地执行以太坊 RPC 调用

```bash
$ cast --help
```

```txt
用法: cast <COMMAND>

命令:
  4byte                  从 https://openchain.xyz 获取给定选择器的函数签名 [别名: 4, 4b]
  4byte-decode           使用 https://openchain.xyz 解码 ABI 编码的 calldata [别名: 4d, 4bd]
  4byte-event            从 https://openchain.xyz 获取给定主题 0 的事件签名 [别名: 4e, 4be, topic0-event, t0e]
  abi-decode             解码 ABI 编码的输入或输出数据 [别名: ad, --abi-decode]
  abi-encode             ABI 编码给定的函数参数，排除选择器 [别名: ae]
  access-list            为交易创建访问列表 [别名: ac, acl]
  address-zero           打印零地址 [别名: --address-zero, az]
  admin                  获取 EIP-1967 管理账户 [别名: adm]
  age                    获取区块的时间戳 [别名: a]
  balance                获取账户的 wei 余额 [别名: b]
  base-fee               获取区块的 basefee [别名: ba, fee, basefee]
  bind                   从给定 ABI 生成 rust 绑定 [别名: bi]
  block                  获取区块的信息 [别名: bl]
  block-number           获取最新的区块号 [别名: bn]
  call                   在账户上执行调用而不发布交易 [别名: c]
  calldata               ABI 编码带参数的函数 [别名: cd]
  calldata-decode        解码 ABI 编码的输入数据 [别名: --calldata-decode, cdd]
  chain                  获取当前链的符号名称
  chain-id               获取以太坊链 ID [别名: ci, cid]
  client                 获取当前客户端版本 [别名: cl]
  code                   获取合约的运行时字节码 [别名: co]
  codehash               获取账户的 codehash
  codesize               获取合约的运行时字节码大小 [别名: cs]
  completions            生成 shell 完成脚本 [别名: com]
  compute-address        从给定的 nonce 和部署者地址计算合约地址 [别名: ca]
  concat-hex             连接十六进制字符串 [别名: --concat-hex, ch]
  create2                使用 CREATE2 生成确定性合约地址 [别名: c2]
  decode-eof             解码 EOF 容器字节
  decode-transaction     解码原始签名的 EIP 2718 类型交易 [别名: dt, decode-tx]
  disassemble            将十六进制编码的字节码反汇编为单个/可读的操作码 [别名: da]
  estimate               估算交易的 gas 成本 [别名: e]
  etherscan-source       从 Etherscan 获取合约的源代码 [别名: et, src]
  find-block             获取与提供的时间戳最接近的区块号 [别名: f]
  format-bytes32-string  将字符串格式化为 bytes32 编码 [别名: --format-bytes32-string]
  from-bin               将二进制数据转换为十六进制数据 [别名: --from-bin, from-binx, fb]
  from-fixed-point       将定点数转换为整数 [别名: --from-fix, ff]
  from-rlp               解码 RLP 十六进制编码的数据 [别名: --from-rlp]
  from-utf8              将 UTF8 文本转换为十六进制 [别名: --from-ascii, --from-utf8, from-ascii, fu, fa]
  from-wei               将 wei 转换为 ETH 数量 [别名: --from-wei, fw]
  gas-price              获取当前 gas 价格 [别名: g]
  generate-fig-spec      生成 Fig 自动补全规范 [别名: fig]
  hash-message           根据 EIP-191 哈希消息 [别名: --hash-message, hm]
  hash-zero              打印零哈希 [别名: --hash-zero, hz]
  help                   打印此消息或给定子命令的帮助
  implementation         获取 EIP-1967 实现账户 [别名: impl]
  index                  计算映射中条目的存储槽 [别名: in]
  index-erc7201          按照 `ERC-7201: Namespaced Storage Layout` 指定计算存储槽 [别名: index7201, in7201]
  interface              从给定 ABI 生成 Solidity 接口 [别名: i]
  keccak                 使用 Keccak-256 哈希任意数据 [别名: k, keccak256]
  logs                   按签名或主题获取日志 [别名: l]
  lookup-address         执行 ENS 反向查找 [别名: la]
  max-int                打印给定整数类型的最大值 [别名: --max-int, maxi]
  max-uint               打印给定整数类型的最大值 [别名: --max-uint, maxu]
  min-int                打印给定整数类型的最小值 [别名: --min-int, mini]
  mktx                   构建并签名交易 [别名: m]
  namehash               计算名称的 ENS namehash [别名: na, nh]
  nonce                  获取账户的 nonce [别名: n]
  parse-bytes32-address  从 bytes32 编码解析校验和地址 [别名: --parse-bytes32-address]
  parse-bytes32-string   从 bytes32 编码解析字符串 [别名: --parse-bytes32-string]
  pretty-calldata        美化打印 calldata [别名: pc]
  proof                  为给定存储槽生成存储证明 [别名: pr]
  publish                将原始交易发布到网络 [别名: p]
  receipt                获取交易的收据 [别名: re]
  resolve-name           执行 ENS 查找 [别名: rn]
  rpc                    执行原始 JSON-RPC 请求 [别名: rp]
  run                    在本地环境中运行已发布的交易并打印跟踪 [别名: r]
  selectors              从字节码中提取函数选择器和参数 [别名: sel]
  send                   签名并发布交易 [别名: s]
  shl                    执行左移操作
  shr                    执行右移操作
  sig                    获取函数的选择器 [别名: si]
  sig-event              从事件字符串生成事件签名 [别名: se]
  storage                获取合约存储槽的原始值 [别名: st]
  storage-root           获取账户的存储根 [别名: sr]
  to-ascii               将十六进制数据转换为 ASCII 字符串 [别名: --to-ascii, tas, 2as]
  to-base                将一个基数的数字转换为另一个基数 [别名: --to-base, --to-radix, to-radix, tr, 2r]
  to-bytes32             将十六进制数据右填充到 32 字节 [别名: --to-bytes32, tb, 2b]
  to-check-sum-address   将地址转换为校验和格式 (EIP-55) [别名: --to-checksum-address, --to-checksum, to-checksum, ta, 2a]
  to-dec                 将一个基数的数字转换为十进制 [别名: --to-dec, td, 2d]
  to-fixed-point         将整数转换为定点数 [别名: --to-fix, tf, 2f]
  to-hex                 将一个基数的数字转换为另一个基数 [别名: --to-hex, th, 2h]
  to-hexdata             将输入标准化为小写，0x 前缀的十六进制 [别名: --to-hexdata, thd, 2hd]
  to-int256              将数字转换为十六进制编码的 int256 [别名: --to-int256, ti, 2i]
  to-rlp                 RLP 编码十六进制数据或十六进制数据数组 [别名: --to-rlp]
  to-uint256             将数字转换为十六进制编码的 uint256 [别名: --to-uint256, tu, 2u]
  to-unit                将 ETH 数量转换为另一单位 (ether, gwei 或 wei) [别名: --to-unit, tun, 2un]
  to-utf8                将十六进制数据转换为 utf-8 字符串 [别名: --to-utf8, tu8, 2u8]
  to-wei                 将 ETH 数量转换为 wei [别名: --to-wei, tw, 2w]
  tx                     获取交易的信息 [别名: t]
  upload-signature       将给定的签名上传到 https://openchain.xyz [别名: ups]
  wallet                 钱包管理工具 [别名: w]

选项:
  -h, --help     打印帮助
  -V, --version  打印版本

在书中找到更多信息:
http://book.getfoundry.sh/reference/cast/cast.html
```