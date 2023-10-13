## 与 VSCode 集成

您可以通过安装 [VSCode Solidity 扩展](https://github.com/juanfranblanco/vscode-solidity) 获得对 Visual Studio Code 的 Solidity 支持。

为了使扩展与 Foundry 兼容，您需要将重映射(remappings)放在 `remappings.txt` 中。 如果它们已经在 `foundry.toml` 中，请复制它们并改用 `remappings.txt`。 如果您只是使用 Foundry 提供的自动生成的重新映射，请运行 `forge remappings > remappings.txt`。

如果您使用非标准项目布局，您可能需要将以下内容添加到您的 `.vscode/settings.json` 中：

```json
{
   "solidity.packageDefaultDependenciesContractsDirectory": "src",
   "solidity.packageDefaultDependenciesDirectory": "lib"
}
```

其中 `src` 是源代码目录，`lib` 是您的依赖目录。

还建议指定 Solidity 编译器版本：

```json
"solidity.compileUsingRemoteVersion"："v0.8.10"
```

要使 Foundry 与所选版本一致，请将以下内容添加到 `foundry.toml` 中的 `default` 配置文件。

```toml
solc_version = "0.8.10"
```