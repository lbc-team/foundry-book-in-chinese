## forge coverage

### 名称

forge coverage - 显示哪些部分的代码已被测试覆盖。

### 概要

`forge coverage` [*选项*]

### 描述

显示哪些部分的代码已被测试覆盖。

> **警告** ⚠️  
> 
> 在 `lcov` 2.0 版本中引入的 `derive function end line` 功能（默认启用）不受支持，因此 `lcov` 和 `genhtml` 应该使用 `--rc derive_function_end_line=0` 选项运行，以生成覆盖率报告。

### 选项

#### 报告选项

`--report` 允许您指定用于覆盖率的报告类型。此标志可以多次使用。

它有三个不同的选项，默认设置为 `summary`。

`summary`  
&nbsp;&nbsp;&nbsp;&nbsp;输出一个图表，显示您的代码有多少百分比被测试覆盖。

`lcov`  
&nbsp;&nbsp;&nbsp;&nbsp;在项目目录的根目录中创建一个包含覆盖率数据的 lcov.info 文件。

`debug`  
&nbsp;&nbsp;&nbsp;&nbsp;输出描述未覆盖代码位置的行。

{{#include common-options.md}}

#### 优化选项

`--ir-minimum` 允许您在覆盖率运行时启用 `via-ir`，以实现所需的 ["最小优化量"](https://github.com/ethereum/solidity/issues/12533#issuecomment-1013073350)。

### 示例

1. 查看汇总覆盖率：

   ```sh
   forge coverage
   ```

2. 创建包含覆盖率数据的 lcov 文件：

   ```sh
   forge coverage --report lcov
   ```

3. 输出未覆盖代码位置：
   ```sh
   forge coverage --report debug
   ```

### 参见

[forge](./forge.md), [forge test](./forge-test.md)
