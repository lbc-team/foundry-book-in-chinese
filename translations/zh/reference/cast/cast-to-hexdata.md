## cast to-hexdata

### 名称

cast-to-hexdata - 将输入规范化为小写字母，带有 0x 前缀的十六进制数。

### 概要

``cast to-hexdata`` [*选项*] *输入*

### 描述

将输入规范化为小写字母，带有 0x 前缀的十六进制数。

输入数据（*input*）可以是以下之一：

- 大小写混合的十六进制数，带有或不带有 0x 前缀。
- 应该被连接的带有 0x 前缀的十六进制数，用`:`分隔。
- 包含十六进制数的文件的绝对路径。
- 一个`@tag`，其中 tag 在环境变量中定义。

### 选项

{{#include common-options.md}}

### 示例

1. 添加 0x 前缀：
    ```sh
    cast to-hexdata deadbeef
    ```

2. 连接十六进制值：
    ```sh
    cast to-hexdata "deadbeef:0xbeef"
    ```

3. 规范化`MY_VAR`中的十六进制值：
    ```sh
    cast to-hexdata "@MY_VAR"
    ```

### 参见

[cast](./cast.md)