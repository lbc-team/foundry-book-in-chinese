# forge update

更新一个或多个依赖项

```bash
$ forge update --help
用法： forge update [OPTIONS] [DEPENDENCIES]...

参数：
  [DEPENDENCIES]...
          The dependencies you want to update

选项：
      --root <PATH>
          The project's root path.
          
          By default root of the Git repository, if in one, or the current working directory.

  -f, --force
          Override the up-to-date check

  -r, --recursive
          Recursively update submodules

  -h, --help
          Print help (see a summary with '-h')
```