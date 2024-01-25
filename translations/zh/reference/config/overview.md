## Config 概述

Foundry 的配置系统允许你对其工具进行配置。

### Profiles

配置可以被任意地命名为配置文件。默认配置文件被命名为 `default`，所有其他配置文件都继承这个配置文件的值。配置文件是在 `profile` 映射中定义的。

要添加一个名称为 `local` 的配置文件，你需要添加：

```toml
[profile.local]
```

你可以使用 `FOUNDRY_PROFILE` 环境变量选择要使用的配置文件。

### 全局配置

你可以在 `~/.foundry` 创建一个 `foundry.toml` 文件来全局配置 Foundry。

### 环境变量

可以通过 `FOUNDRY_` 和 `DAPP_` 前缀的环境变量来覆盖配置。

如下所示：

- `FOUNDRY_FFI`, `DAPP_FFI`, `DAPP_TEST_FFI`
- `FOUNDRY_PROFILE`
- `FOUNDRY_REMAPPINGS`, `DAPP_REMAPPINGS`
- `FOUNDRY_LIBRARIES`, `DAPP_LIBRARIES`
- `FOUNDRY_FS_PERMISSIONS`, `DAPP_FS_PERMISSIONS`, `DAPP_TEST_FS_PERMISSIONS`
- `DAPP_TEST_CACHE`
- `DAPP_TEST_FUZZ_RUNS`
- `DAPP_TEST_FUZZ_DEPTH`

### Configuration format

配置文件是以 [TOML 格式](https://toml.io) 编写的，章节内有简单的键-值对。

本页详细描述了每一个配置项。 查看默认值，可以参考本文中的特定键, 或参阅 [default config](/static/config.default.toml).

### Configuration keys

本节记录所有配置键。所有配置键必须位于一个配置文件下，如 `default`。
