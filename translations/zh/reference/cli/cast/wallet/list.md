# cast wallet list

列出密钥库默认目录中的所有账户

```bash
$ cast wallet list --help
```

```txt
用法：cast wallet list [OPTIONS]

选项：
      --dir [<DIR>]
          List all the accounts in the keystore directory. Default keystore
          directory is used if no path provided
  -l, --ledger
          List accounts from a Ledger hardware wallet
  -t, --trezor
          List accounts from a Trezor hardware wallet
      --aws
          List accounts from AWS KMS
      --all
          List all configured accounts
  -m, --max-senders <MAX_SENDERS>
          Max number of addresses to display from hardware wallets [default: 3]
  -h, --help
          Print help
```