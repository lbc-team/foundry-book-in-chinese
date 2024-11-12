# cast calldata-decode

解码 ABI 编码的输入数据。

```bash
$ cast calldata-decode --help
```

```txt
用法：cast calldata-decode [OPTIONS] <SIG> <CALLDATA>

参数：
  <SIG>
          The function signature in the format `<name>(<in-types>)(<out-types>)`

  <CALLDATA>
          The ABI-encoded calldata

选项：
  -h, --help
          Print help (see a summary with '-h')

Display options:
  -j, --json
          Print the decoded calldata as JSON
```