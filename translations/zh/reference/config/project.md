## Project

通常与项目相关的配置。

##### `src`

- 类型: string
- 默认值: src
- 环境变量: `FOUNDRY_SRC` 或 `DAPP_SRC`

相对于项目根目录的合约源代码的路径。

##### `test`

- 类型: string
- 默认值: test
- 环境变量: `FOUNDRY_TEST` 或 `DAPP_TEST`

相对于项目根目录的合约测试源代码的路径。

##### `script`

- 类型：字符串
- 默认值：script
- 环境：`FOUNDRY_SCRIPT` 或 `DAPP_SCRIPT`

脚本合约源文件相对于项目根目录的路径。

##### `out`

- 类型: string
- 默认值: out
- 环境变量: `FOUNDRY_OUT` 或 `DAPP_OUT`

放置合约输出的路径，相对于项目根目录。

##### `libs`

- 类型: 字符串数组 (路径)
- 默认值: lib
- 环境变量: `FOUNDRY_LIBS` 或 `DAPP_LIBS`

一个包含库的路径数组，相对于项目根目录。

##### `cache`

- 类型: boolean
- 默认值: true
- 环境变量: `FOUNDRY_CACHE` 或 `DAPP_CACHE`

是否启用缓存。如果启用，编译源代码、测试和依赖关系的结果将缓存在 `cache` 中。

##### `cache_path`

- 类型: string
- 默认值: cache
- 环境变量: `FOUNDRY_CACHE_PATH` 或 `DAPP_CACHE_PATH`

缓存的路径，相对于项目根目录。

##### `broadcast`

- 类型: string
- 默认值: broadcast

广播交易日志的路径，相对于项目根目录。

##### `force`

- 类型: boolean
- 默认值: false
- 环境变量: `FOUNDRY_FORCE` 或 `DAPP_FORCE`

是否执行干净构建，丢弃缓存。
