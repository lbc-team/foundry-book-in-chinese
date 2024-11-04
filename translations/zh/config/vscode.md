## 与 VSCode 集成

您可以通过安装 [VSCode Solidity 扩展](https://github.com/juanfranblanco/vscode-solidity) 获得对 Visual Studio Code 的 Solidity 支持。

要使该扩展与 Foundry 协同工作，您可能需要调整一些设置。

### 1. 重映射

您可能希望将重映射放置在 `remappings.txt` 中。

如果它们已经在 `foundry.toml` 中，请将其复制并改用 `remappings.txt`。如果您只是使用 Foundry 提供的自动生成的重映射，请运行 `forge remappings > remappings.txt`。

### 2. 依赖项

您可能需要将以下内容添加到您的 `.vscode/settings.json` 中，以便扩展程序找到您的依赖项：

```json
{
  "solidity.packageDefaultDependenciesContractsDirectory": "src",
  "solidity.packageDefaultDependenciesDirectory": "lib"
}
```

其中 `src` 是源代码目录，`lib` 是您的依赖项目录。

### 3. 格式化程序

要启用 Foundry 提供的内置格式化程序在保存时自动格式化您的代码，您可以将以下设置添加到您的 `.vscode/settings.json` 中：

```json
{
  "editor.formatOnSave": true,
  "[solidity]": {
    "editor.defaultFormatter": "JuanBlanco.solidity" 
  },
  "solidity.formatter": "forge",
}
```

要配置格式化程序设置，请参阅 [Formatter](../reference/config/formatter.md) 参考。

### 4. Solc 版本

最后，建议指定一个 Solidity 编译器版本：

```json
"solidity.compileUsingRemoteVersion": "v0.8.17"
```

要使 Foundry 与所选版本保持一致，请将以下内容添加到 `foundry.toml` 中的 `default` 配置文件中。

```toml
solc = "0.8.17"
```

### 使用 OpenZeppelin 合约和非标准项目布局的示例。

```bash
.
└── project
└── contracts
═── lib
│ ═── forge-std
│ └── openzeppelin-contracts
═── script
═── src
└── test
```

在 `remappings.txt` 文件中添加一行 ([`forge remapping`](../projects/dependencies.md#remapping-dependencies)):

```solidity
@openzeppelin/=lib/openzeppelin-contracts/
```

在 `.vscode/settings.json` 文件中添加一行 (solidity 扩展设置):

```json
{
"solidity.remappings": [
"@openzeppelin/=project/contracts/lib/openzeppelin-contracts/"
]
}
```

现在可以使用 OpenZeppelin 文档中的所有合约。

```javascript 从 "@openzeppelin/contracts/token/ERC20/ERC20.sol" 导入 {ERC20}；
````