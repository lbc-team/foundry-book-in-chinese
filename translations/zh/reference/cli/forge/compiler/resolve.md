# forge compiler resolve

检索项目中编译器的已解析版本

```bash
$ forge compiler resolve --help
```

```txt
用法: forge compiler resolve [OPTIONS]

选项:
  -r, --root <PATH>
          根目录

  -s, --skip <REGEX>
          跳过匹配给定正则表达式模式的文件

  -h, --help
          打印帮助信息（使用 '-h' 查看摘要）

显示选项:
  -v, --verbosity...
          输出的详细程度。
          
          多次传递以增加详细程度（例如 -v, -vv, -vvv）。
          
          详细程度级别:
          - 0: 打印编译器版本。
          - 1: 打印编译器版本和源路径。
          - 2: 打印编译器版本、源路径和编译器支持的最大 EVM 版本。

  -j, --json
          以 JSON 格式打印
```