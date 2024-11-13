## 依赖

默认情况下，Forge 使用 [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) 管理依赖项，这意味着它可以与任何包含智能合约的 GitHub 代码库一起使用。

### 添加依赖

要添加依赖项，请运行 [`forge install`](../reference/forge/forge-install.md)：

```sh
{{#include ../output/deps/forge-install:all}}
```

这将拉取 `solmate` 库，在 git 中暂存 `.gitmodules` 文件并使用消息“Installed solmate”进行提交。

如果我们现在检查 `lib` 文件夹：

```sh
{{#include ../output/deps/tree:all}}
```

我们可以看到Forge安装了`solmate`！

默认情况下，`forge install` 安装最新的 master 分支版本。 如果你想安装一个特定的标签或提交，你可以这样做：

```sh
$ forge install transmission11/solmate@v7
```

### 重新映射依赖项

Forge 可以重新映射(remap)依赖关系，使它们更容易导入。 Forge 将自动尝试为你推断出一些重新映射：

```sh
{{#include ../output/deps/forge-remappings:all}}
```

这些重新映射意味着：

- 要从 `forge-std` 导入，我们会这样写：`import "forge-std/Contract.sol";`
- 要从 `ds-test` 导入，我们会这样写：`import "ds-test/Contract.sol";`
- 要从 `solmate` 导入，我们会这样写：`import "solmate/Contract.sol";`
- 要从 `weird-erc20` 导入，我们会这样写：`import "weird-erc20/Contract.sol";`

你可以通过在项目的根目录中创建一个 `remappings.txt` 文件来自定义这些重新映射。

让我们创建一个名为 `solmate-utils` 的重映射，它指向 solmate repo中的 `utils` 文件夹！

```sh
@solmate-utils/=lib/solmate/src/utils/
```

你还可以在 `foundry.toml` 中设置重映射。

```toml
remappings = [
    "@solmate-utils/=lib/solmate/src/utils/",
]
```

现在我们可以像这样导入 solmate repo的 `src/utils` 中的任何合约：

```solidity
import {LibString} from "@solmate-utils/LibString.sol";
```

### 更新依赖

你可以使用 [`forge update <dep>`](../reference/forge/forge-update.md) 将特定依赖项更新为你指定版本的最新提交。 例如，如果我们想从我们之前安装的 `solmate` 主版本中提取最新的提交，我们将运行：

```sh
$ forge update lib/solmate
```

或者，你可以通过运行 `forge update` 一次对所有依赖项执行更新。

### 删除依赖

你可以使用 [`forge remove <deps>...`](../reference/forge/forge-remove.md) 删除依赖项，其中 `<deps>` 是依赖项的完整路径或只是名称 . 例如，要删除 `solmate`，这两个命令是等价的：

```ignore
$ forge remove solmate
# ... 等同于 ...
$ forge remove lib/solmate
```

### 与 Hardhat 兼容

Forge 还支持基于 Hardhat 的项目，其中依赖项是 npm 包（存储在 `node_modules` 中）并且其合约存储在 `contracts` 中而不是 `src` 中。

要启用 Hardhat 兼容模式，请传递 `--hh`  标志。