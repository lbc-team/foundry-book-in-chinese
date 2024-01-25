# cast upload-signature

将给定的签名上传到 https://openchain.xyz。

```bash
$ cast upload-signature --help
Usage: cast upload-signature [SIGNATURES]...

Arguments:
  [SIGNATURES]...
          The signatures to upload.
          
          Prefix with 'function', 'event', or 'error'. Defaults to function if no prefix given. Can also take paths to contract artifact JSON.

Options:
  -h, --help
          Print help (see a summary with '-h')
```