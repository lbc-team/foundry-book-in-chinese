# cast abi-decode

解码 ABI 编码的输入或输出数据。

```bash
$ cast abi-decode --help
```

```txt
用法：cast abi-decode [OPTIONS] <SIG> <CALLDATA>

参数：
  <SIG>
          The function signature in the format `<name>(<in-types>)(<out-types>)`

  <CALLDATA>
          The ABI-encoded calldata

选项：
  -h, --help
          Print help (see a summary with '-h')

Decode input data instead of output data:
  -i, --input
          Whether to decode the input or output data

Display options:
  -j, --json
          Print the decoded calldata as JSON
```