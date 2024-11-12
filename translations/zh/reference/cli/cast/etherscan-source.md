# 获取 Etherscan 上合约的源代码

```bash
$ cast etherscan-source --help
```

```txt
用法：cast etherscan-source [OPTIONS] <ADDRESS>

参数：
  <ADDRESS>  The contract's address

选项：
  -f, --flatten
          Whether to flatten the source code
  -d <DIRECTORY>
          The output directory/file to expand source tree into
  -e, --etherscan-api-key <KEY>
          The Etherscan (or equivalent) API key [env: ETHERSCAN_API_KEY=]
  -c, --chain <CHAIN>
          The chain name or EIP-155 chain ID [env: CHAIN=]
  -h, --help
          Print help
```