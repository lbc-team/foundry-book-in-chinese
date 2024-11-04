# forge soldeer push

将依赖项推送到仓库

```bash
$ forge soldeer push --help
```

```txt
用法: forge soldeer push [OPTIONS] <DEPENDENCY>~<VERSION> [PATH]

参数:
  <DEPENDENCY>~<VERSION>
          依赖项名称和版本，用波浪号分隔。
          
          当你想将依赖项推送到中央仓库时，应始终使用此格式: `<https://soldeer.xyz>`。

  [PATH]
          如果你要推送的包不在当前目录中，请使用此选项。
          
          示例: `soldeer push mypkg~0.1.0 /path/to/dep`。

选项:
  -d, --dry-run
          如果设置，则不发布包，但生成一个可供检查的 zip 文件

      --skip-warnings
          如果你想跳过在尝试推送 .env 等点文件时可能触发的警告，请使用此选项

  -h, --help
          打印帮助信息（查看摘要请使用 '-h'）

有关更多信息，请阅读 README.md
```