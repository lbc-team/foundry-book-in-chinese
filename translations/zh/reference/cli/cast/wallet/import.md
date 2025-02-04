# 导入钱包

将私钥导入加密的密钥库

```bash
$ cast wallet import --help
```

```txt
用法：cast wallet import [OPTIONS] <ACCOUNT_NAME>

参数：
  <ACCOUNT_NAME>
          The name for the account in the keystore

选项：
  -k, --keystore-dir <KEYSTORE_DIR>
          If provided, keystore will be saved here instead of the default
          keystores directory (~/.foundry/keystores)

      --unsafe-password <PASSWORD>
          Password for the JSON keystore in cleartext This is unsafe, we
          recommend using the default hidden password prompt
          
          [env: CAST_UNSAFE_PASSWORD=]

  -h, --help
          Print help (see a summary with '-h')

Wallet options - raw:
  -i, --interactive
          Open an interactive prompt to enter your private key

      --private-key <RAW_PRIVATE_KEY>
          Use the provided private key

      --mnemonic <MNEMONIC>
          Use the mnemonic phrase of mnemonic file at the specified path

      --mnemonic-passphrase <PASSPHRASE>
          Use a BIP39 passphrase for the mnemonic

      --mnemonic-derivation-path <PATH>
          The wallet derivation path.
          
          Works with both --mnemonic-path and hardware wallets.

      --mnemonic-index <INDEX>
          Use the private key from the given mnemonic index.
          
          Used with --mnemonic-path.
          
          [default: 0]
```