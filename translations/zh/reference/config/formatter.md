## Formatter

与 Forge 格式化程序行为相关的配置。这些键都位于 `[fmt]` 部分下。

##### `line_length`

- 类型：数字
- 默认值：120
- 环境变量：`FOUNDRY_FMT_LINE_LENGTH` 或 `DAPP_FMT_LINE_LENGTH`

格式化程序尝试换行的最大行长度。

##### `tab_width`

- 类型：数字
- 默认值：4
- 环境变量：`FOUNDRY_FMT_TAB_WIDTH` 或 `DAPP_FMT_TAB_WIDTH`

每个缩进级别的空格数。

##### `bracket_spacing`

- 类型：布尔
- 默认值：false
- 环境变量：`FOUNDRY_FMT_BRACKET_SPACING` 或 `DAPP_FMT_BRACKET_SPACING`

是否在括号之间打印空格。

##### `int_types`

- 类型：字符串
- 默认值：`long`
- 环境变量：`FOUNDRY_FMT_INT_TYPES` 或 `DAPP_FMT_INT_TYPES`

uint/int256 类型的样式。有效值包括：

- `long`（默认）：使用显式的 `uint256` 或 `int256`
- `short`：使用隐式的 `uint` 或 `int`
- `preserve`：使用源代码中定义的类型

##### `multiline_func_header`

- 类型：字符串
- 默认值：`attributes_first`
- 环境变量：`FOUNDRY_FMT_MULTILINE_FUNC_HEADER` 或 `DAPP_FMT_MULTILINE_FUNC_HEADER`

多行函数头的样式，如果不适合一行。有效值包括：

- `attributes_first`（默认）：首先将函数属性写成多行
- `params_first`：首先将函数参数写成多行
- `all`：如果函数参数或属性是多行的，则全部多行

##### `quote_style`

- 类型：字符串
- 默认值：`double`
- 环境变量：`FOUNDRY_FMT_QUOTE_STYLE` 或 `DAPP_FMT_QUOTE_STYLE`

定义引号样式。有效值包括：

- `double`（默认）：在可能的情况下使用双引号（`"`）
- `single`：在可能的情况下使用单引号（`'`）
- `preserve`：使用源代码中定义的引号

##### `number_underscore`

- 类型：字符串
- 默认值：`preserve`
- 环境变量：`FOUNDRY_FMT_NUMBER_UNDERSCORE` 或 `DAPP_FMT_NUMBER_UNDERSCORE`

数字文字中下划线的样式。有效值包括：

- `preserve`（默认）：使用源代码中定义的下划线
- `thousands`：每千位添加一个下划线，如果大于 9999。例如，`1000` 格式化为 `1000`，`10000` 格式化为 `10_000`
- `remove`：移除所有下划线

##### `override_spacing`

- 类型：布尔
- 默认值：true
- 环境变量：`FOUNDRY_FMT_OVERRIDE_SPACING` 或 `DAPP_FMT_OVERRIDE_SPACING`

是否在合约状态变量、函数和修饰符的`override`属性中打印空格。

##### `wrap_comments`

- 类型：布尔
- 默认值：false
- 环境变量：`FOUNDRY_FMT_WRAP_COMMENTS` 或 `DAPP_FMT_WRAP_COMMENTS`

是否在达到`line_length`时换行注释。

##### `ignore`

- 类型：字符串数组（模式）
- 默认值：`[]`
- 环境变量：`FOUNDRY_FMT_IGNORE` 或 `DAPP_FMT_IGNORE`

在格式化时要忽略的文件列表。这是一个逗号分隔的 glob 模式列表。