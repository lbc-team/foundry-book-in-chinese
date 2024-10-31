## Soldeer 作为包管理器

如 [这里](./dependencies) 所述，Foundry 一直以来使用 git 子模块来处理依赖关系。

随着项目变得越来越复杂，原生包管理器的需求开始出现。

一种新的方法正在开发中，[soldeer.xyz](https://soldeer.xyz)，这是一个用 Rust 构建的 Solidity 原生依赖管理器，并且是开源的（查看仓库 [https://github.com/mario-eth/soldeer](https://github.com/mario-eth/soldeer)）。

### 初始化新项目
如果你在新的 Foundry 项目中第一次使用 Soldeer，可以使用 `init` 命令安装一个全新的 Soldeer 实例，包含必要的配置和最新版本的 `forge-std`。
```bash
forge soldeer init
```

### 添加依赖

#### 添加存储在中央仓库中的依赖

要添加依赖，可以访问 [soldeer.xyz](https://soldeer.xyz) 并搜索你想要添加的依赖（例如，openzeppelin 5.0.2）。

![image](https://i.postimg.cc/Hm6R8MTs/Unknown-413.png)

然后只需运行 forge 命令：
```bash
forge soldeer install @openzeppelin-contracts~5.0.2
```

这将从中央仓库下载依赖并将其安装到 `dependencies` 目录中。

Soldeer 可以管理两种类型的依赖配置：使用 `soldeer.toml` 或嵌入在 `foundry.toml` 中。为了与 Foundry 一起使用，必须在 `foundry.toml` 中定义 `[dependencies]` 配置。这将告诉 `soldeer CLI` 在那里定义已安装的依赖。
例如：

```toml
# Full reference https://github.com/foundry-rs/foundry/tree/master/crates/config

[profile.default]
auto_detect_solc = false 
bytecode_hash = "none" 
fuzz = { runs = 1_000 } 
libs = ["dependencies"] # <= This is important to be added
gas_reports = ["*"] 

[dependencies] # <= Dependencies will be added under this config
"@openzeppelin-contracts" = { version = "5.0.2" }
"@uniswap-universal-router" = { version = "1.6.0" }
"@prb-math" = { version = "4.0.2" }
forge-std = { version = "1.8.1" }
```

#### 添加存储在特定链接中的依赖

如果中央仓库没有某个依赖，可以通过提供 zip 压缩包链接来安装它。

例如：
```bash
forge soldeer install @custom-dependency~1.0.0 https://my-website.com/custom-dependency-1-0-0.zip
```

上述命令将尝试从提供的链接下载依赖并将其安装为普通依赖。为此，你将在配置中看到一个名为 `path` 的额外字段。

例如：
```toml
[dependencies]
"@custom-dependency" = { version = "1.0.0", path = "https://my-website.com/custom-dependency-1-0-0.zip" }
```

#### 添加存储在 GIT 中的依赖
如果你选择使用 Git 作为依赖的来源——尽管我们通常不鼓励这样做，因为 Git 并不是为依赖管理而设计的——你可以将 Git 仓库链接作为额外参数提供。Soldeer 将自动使用 Git 子进程处理安装。
例如：
```bash
forge soldeer install forge-std~1.9.2 https://github.com/foundry-rs/forge-std.git
```

如果你想使用特定的修订版、分支或标签，可以通过将以下参数附加到命令中来实现：`--rev/--tag/--branch`

例如：
```bash
forge soldeer install forge-std~1.9.2 https://github.com/foundry-rs/forge-std.git --rev 4695fac44b2934aaa6d7150e2eaf0256fdc566a7
```

### 更新依赖

因为 Soldeer 在配置文件（foundry 或 soldeer toml）中指定了依赖，所以在团队内共享依赖配置要容易得多。

例如，在 git 仓库中拥有这个 Foundry 配置文件后，可以拉取该仓库，然后运行 `forge soldeer update`。此命令将自动安装在 `[dependencies]` 标签下指定的所有依赖。

```toml
# Full reference https://github.com/foundry-rs/foundry/tree/master/crates/config

[profile.default]
auto_detect_solc = false 
bytecode_hash = "none" 
fuzz = { runs = 1_000 } 
libs = ["dependencies"] # <= This is important to be added
gas_reports = ["*"]

[dependencies] # <= Dependencies will be added under this config
"@openzeppelin-contracts" = { version = "5.0.2" }
"@uniswap-universal-router" = { version = "1.6.0" }
"@prb-math" = { version = "4.0.2" }
forge-std = { version = "1.8.1" }
```

### 移除依赖

你可以使用 `forge soldeer uninstall DEPENDENCY`。

示例：`forge soldeer uninstall @openzeppelin-contracts`。此操作将移除：
- 配置条目
- `dependencies` 工件
- `soldeer.lock` 条目
- `remappings` 条目（txt 或配置重映射）

此外，你还可以通过简单地删除工件：依赖文件、配置条目、重映射条目，手动移除依赖。

### 重映射

重映射现在是完全可配置的，配置 TOML 文件（foundry.toml）接受一个 `[soldeer]` 字段，具有以下选项

```toml
[soldeer]
# whether soldeer manages remappings
remappings_generate = true

# whether soldeer re-generates all remappings when installing, updating or uninstalling deps
remappings_regenerate = false

# whether to suffix the remapping with the version: `name-a.b.c`
remappings_version = true

# a prefix to add to the remappings ("@" would give `@name`)
remappings_prefix = ""

# where to store the remappings ("txt" for `remappings.txt` or "config" for `foundry.toml`)
# ignored when `soldeer.toml` is used as config (uses `remappings.txt`)
remappings_location = "txt"
```

### 安装依赖的依赖，即子依赖

每当你安装一个依赖时，该依赖可能还有其他依赖需要安装。目前，你可以通过在配置文件中指定 `recursive_deps` 字段作为配置条目，或者在运行安装或更新命令时传递 `--recursive-deps` 参数来处理此问题。这将确保所有必要的子依赖被自动拉入。
例如：
```toml
[soldeer]
recursive_deps = true
```

### 将新版本推送到中央仓库

Soldeer 的作用类似于 npmjs/crates.io，鼓励所有开发者将他们的项目发布到中央仓库。

为此，你需要访问 [soldeer.xyz](https://soldeer.xyz)，创建一个帐户，验证它，然后

![image](https://i.postimg.cc/G3VDpN2S/s1.png) 

只需添加一个新项目

![image](https://i.postimg.cc/rsBRYd3L/s2.png) 

项目创建后，你可以进入项目源并：
- 创建一个 `.soldeerignore` 文件，作为 `.gitignore` 来排除不需要的文件。`.gitignore` 文件也会被尊重。
- 运行 `forge soldeer login` 登录到你的帐户。
- 在你想要推送到与项目 `my-project` 关联的中央仓库的目录中运行 `forge soldeer push my-project~1.0.0`，版本为 `1.0.0`。

如果你想推送特定目录而不是当前终端所在的目录，可以使用 `forge soldeer push my-project~1.0.0 /path/to/directory`。

**警告** ⚠️

你有可能将敏感文件推送到中央仓库，所有人都可以看到。确保在 `.soldeerignore` 文件中排除敏感文件。
此外，我们实现了一个警告，如果你尝试推送包含任何 `.dot` 文件/目录的项目，将会触发该警告。
如果你想跳过此警告，可以直接使用
```bash
forge soldeer push my-project~1.0.0 --skip-warnings
```

#### 干运行
如果你想模拟推送版本时会发生什么，可以使用 `--dry-run` 标志。这将创建一个 zip 文件，你可以在推送到中央仓库之前检查它。

```bash
forge soldeer push my-project~1.0.0 --dry-run
```

#### 登录数据
默认情况下，Soldeer 将登录令牌保存在 `~/.soldeer/.soldeer_login` 文件中，该文件用于将文件推送到中央仓库。如果你希望将令牌保存在其他位置，可以设置环境变量 `SOLDEER_LOGIN_FILE`。

> **警告** ⚠️
> - 一旦创建项目，就无法删除。
> - 一旦推送版本，就无法删除。
> - 你不能推送相同的版本两次。
> - 你在终端中运行的命令中的项目名称必须与在 Soldeer 网站上创建的项目名称匹配。
> - 我们鼓励每个人在将其导入合约时使用版本固定，这将通过确切知道你使用的依赖版本来帮助保护你的代码。此外，这将帮助安全研究人员的工作。
> - 如果你运行干运行，请确保在推送版本之前删除此 zip 文件。
例如，使用
`import '@openzeppelin-contracts/token/ERC20.sol'` 你应该使用
`import '@openzeppelin-contracts-5.0.2/token/ERC20.sol'`

### 如果某个包在中央仓库中不存在，会发生什么？
- 如果某个包在中央仓库中不存在，你可以在 [Soldeer 仓库](https://github.com/mario-eth/soldeer/issues) 中打开一个问题，团队将考虑添加它。
- 如果你有一个想要使用的包，但它不在中央仓库中，你可以按照上述步骤将其推送到中央仓库。