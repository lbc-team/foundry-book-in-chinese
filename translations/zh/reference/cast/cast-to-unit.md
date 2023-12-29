## cast to-unit

### 名称

cast-to-unit - 将以太币金额转换为其他单位。

### 概要

``cast to-unit`` [*选项*] *值* [*单位*]

### 描述

将以太币金额转换为其他单位。

要转换的值（*value*）可以是以太币数量（以 wei 为单位），或带有单位的数字。

有效的单位包括：

- `ether`
- `gwei`
- `wei`

### 选项

{{#include common-options.md}}

### 示例

1. 将 1000 wei 转换为 gwei
    ```sh
    cast to-unit 1000 gwei
    ```

2. 将 1 eth 转换为 gwei
    ```sh
    cast to-unit 1ether gwei
    ```

### 另请参阅

[cast](./cast.md) 
