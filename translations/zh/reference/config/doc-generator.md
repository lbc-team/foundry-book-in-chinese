## 文档生成器


与 Forge 文档生成器行为相关的配置。这些键设置在 `[doc]` 部分。

##### `out`

- 类型：字符串
- 默认值：`docs`
- 环境变量：`FOUNDRY_DOC_OUT`

生成文档的输出路径。

##### `title`

- 类型：字符串
- 环境变量：`FOUNDRY_DOC_TITLE`

生成文档的标题。

##### `book`

- 类型：字符串
- 默认值：`./book.toml`
- 环境变量：`FOUNDRY_DOC_BOOK`

用户提供的 `book.toml` 的路径。在文档生成期间，它将与默认设置合并。

##### `repository`

- 类型：字符串
- 环境变量：`FOUNDRY_DOC_REPOSITORY`

git 仓库的 URL。用于提供到 git 源文件的链接。
如果缺失，`forge` 将尝试查找当前的 origin url 并使用其值。

##### `ignore`

- 类型：字符串数组（模式）
- 默认值：`[]`
- 环境变量：`FOUNDRY_DOC_IGNORE`

在生成文档时要忽略的文件列表。这是一个逗号分隔的 glob 模式列表。
