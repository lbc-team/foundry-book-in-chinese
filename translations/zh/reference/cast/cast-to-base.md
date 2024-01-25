## cast to-base

### 名称

cast-to -base - 将一个基数的数字转换为另一个基数。

### 概要

``cast to-base`` [*选项*] *值* *基数*

### 描述

将一个基数的数字转换为另一个基数。

### 选项

#### 基数选项

`--base-in` *base*
&nbsp;&nbsp;&nbsp;&nbsp;输入数字的基数。可用选项：

&nbsp;&nbsp;&nbsp;&nbsp;10, d, dec, 十进制

&nbsp;&nbsp;&nbsp;&nbsp;16, h, hex, 十六进制

{{#include common-options.md}}

### 示例

1. 将十进制数 64 转换为十六进制
    ```sh
    cast to-base 64 hex
    ```

2. 将十六进制数 100 转换为二进制
    ```sh
    cast to-base 0x100 2
    ```

> 注意：--base-in 参数不是强制的，但如果输入模糊不清，则会需要它。

### 参见

[cast](./cast.md) 
