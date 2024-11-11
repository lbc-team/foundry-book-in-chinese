# cast wallet decrypt-keystore

解密一个密钥库文件以获取私钥

```bash
$ cast wallet decrypt-keystore --help
```

```txt
用法： cast wallet decrypt-keystore [OPTIONS] <ACCOUNT_NAME>

参数：
  <ACCOUNT_NAME>  The name for the account in the keystore

选项：
  -k, --keystore-dir <KEYSTORE_DIR>
          If not provided, keystore will try to be located at the default
          keystores directory (~/.foundry/keystores)
      --unsafe-password <PASSWORD>
          Password for the JSON keystore in cleartext This is unsafe, we
          recommend using the default hidden password prompt [env:
          CAST_UNSAFE_PASSWORD=]
  -h, --help
          Print help
```