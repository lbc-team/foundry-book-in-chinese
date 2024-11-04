# forge soldeer install

安装依赖

```bash
$ forge soldeer install --help
```

```txt
用法: forge soldeer install [OPTIONS] [DEPENDENCY~VERSION] [URL]

参数:
  [DEPENDENCY~VERSION]
          依赖名称和版本，用波浪号分隔。版本始终是必需的。
          
          如果不存在，此命令将安装所有缺失的依赖。

  [URL]
          依赖 zip 文件的 URL。
          
          如果不存在，包将从 Soldeer 仓库中安装。
          
          示例: https://my-domain/dep.zip

选项:
      --rev <REV>
          Git 提交哈希

      --tag <TAG>
          Git 标签

      --branch <BRANCH>
          Git 分支

  -g, --regenerate-remappings
          如果设置，此命令将删除现有的 remappings 并重新创建它们

  -d, --recursive-deps
          如果设置，此命令将递归安装依赖（通过 git 子模块或通过 soldeer）

      --clean
          通过重新安装所有依赖执行干净安装

  -h, --help
          打印帮助信息（查看摘要使用 '-h'）

有关更多信息，请阅读 README.md
```