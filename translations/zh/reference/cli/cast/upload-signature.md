# cast upload-signature

将给定的签名上传到 https://openchain.xyz。

```bash
$ cast upload-signature --help
```

```txt
用法：cast upload-signature [SIGNATURES]...

参数：
  [SIGNATURES]...
          The signatures to upload.
          
          Prefix with 'function', 'event', or 'error'. Defaults to function if
          no prefix given. Can also take paths to contract artifact JSON.

选项：
  -h, --help
          Print help (see a summary with '-h')
```