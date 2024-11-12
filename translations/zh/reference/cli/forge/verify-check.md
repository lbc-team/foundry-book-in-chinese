# forge verify-check

检查在 Etherscan 上的验证状态

```bash
$ forge verify-check --help
```

```txt
用法：forge verify-check [OPTIONS] <ID>

参数：
  <ID>
          The verification ID.
          
          For Etherscan - Submission GUID.
          
          For Sourcify - Contract Address.

选项：
      --retries <RETRIES>
          Number of attempts for retrying verification
          
          [default: 5]

      --delay <DELAY>
          Optional delay to apply in between verification attempts, in seconds
          
          [default: 5]

  -e, --etherscan-api-key <KEY>
          The Etherscan (or equivalent) API key
          
          [env: ETHERSCAN_API_KEY=]

  -c, --chain <CHAIN>
          The chain name or EIP-155 chain ID
          
          [env: CHAIN=]

  -h, --help
          Print help (see a summary with '-h')

Verifier options:
      --verifier <VERIFIER>
          The contract verification provider to use
          
          [default: etherscan]
          [possible values: etherscan, sourcify, blockscout, oklink]

      --verifier-url <VERIFIER_URL>
          The verifier URL, if using a custom provider
          
          [env: VERIFIER_URL=]
```