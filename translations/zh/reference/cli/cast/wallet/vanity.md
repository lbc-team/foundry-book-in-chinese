# 生成钱包个性化地址

生成一个个性化地址

```bash
$ cast wallet vanity --help
```

```txt
用法：cast wallet vanity [OPTIONS]

选项：
      --starts-with <PATTERN>
          Prefix regex pattern or hex string

      --ends-with <PATTERN>
          Suffix regex pattern or hex string

      --nonce <NONCE>
          Generate a vanity contract address created by the generated keypair
          with the specified nonce

      --save-path <PATH>
          Path to save the generated vanity contract address to.
          
          If provided, the generated vanity addresses will appended to a JSON
          array in the specified file.

  -h, --help
          Print help (see a summary with '-h')
```