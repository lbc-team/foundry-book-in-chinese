# cast etherscan-source

从 Etherscan 获取合约源代码

```bash
$ cast etherscan-source --help
Usage: cast etherscan-source [OPTIONS] <ADDRESS>

Arguments:
  <ADDRESS>  The contract's address

Options:
  -d <DIRECTORY>                 The output directory to expand source tree into
  -e, --etherscan-api-key <KEY>  The Etherscan (or equivalent) API key [env: ETHERSCAN_API_KEY=]
  -c, --chain <CHAIN>            The chain name or EIP-155 chain ID [env: CHAIN=]
  -h, --help                     Print help
```