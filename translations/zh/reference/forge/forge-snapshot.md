## forge snapshot

### 名称

forge-snapshot - 创建每个测试的 Gas 使用快照。

### 简介

``forge snapshot`` [*options*]

### 描述

创建每个测试的 Gas 使用快照。

结果被写入一个名为 `.gas-snapshot` 的文件中。你可以通过传递 `--snap <PATH>` 来改变该文件的名称。

在快照中默认包括模糊测试。它们使用一个静态种子来实现确定性的结果。

快照可以用 `--diff` 和 `--check` 来比较。第一个标志将输出一个差异，第二个标志将输出一个差异 *同时* 如果快照不匹配，则以代码 1 退出。

### 选项

#### 快照选项

`--asc`  
按所用 Gas 对结果进行排序（升序）。

`--desc`  
&nbsp;&nbsp;&nbsp;&nbsp;按所用 Gas 对结果进行排序（降序）。

`--min` *min_gas*  
&nbsp;&nbsp;&nbsp;&nbsp;只包括使用了超过给定数量的 Gas 的测试。

`--max` *max_gas*  
&nbsp;&nbsp;&nbsp;&nbsp;只包括使用了小于给定数量的 Gas 的测试。

`--tolerance` *threshold*  
&nbsp;&nbsp;&nbsp;&nbsp;容忍指定百分比范围内的 gas 偏差 (0-100)。

`--diff` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;输出一个与预先存在的快照的差异。

&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，比较是通过 `.gas-snapshot` 完成的。

`--check` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;与预先存在的快照进行比较，如果它们不匹配，则以代码 1 退出。

&nbsp;&nbsp;&nbsp;&nbsp;如果快照不匹配，则输出一个差异。

&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，比较是通过 `.gas-snapshot` 完成的。

`--snap` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;快照的输出文件。默认：`.gas-snapshot`。

{{#include test-options.md}}

{{#include evm-options.md}}

{{#include executor-options.md}}

{{#include core-build-options.md}}

{{#include ../common/display-options.md}}

{{#include common-options.md}}

### 例子

1. 创建一个快照：
    ```sh
    forge snapshot
    ```

2. 生成一个差异：
    ```sh
    forge snapshot --diff
    ```

3. 检查快照是否匹配：
    ```sh
    forge snapshot --check
    ```

### 另请参阅

[forge](./forge.md), [forge test](./forge-test.md)
