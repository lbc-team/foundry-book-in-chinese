## cast basefee

### 名称

cast-base-fee - 获取一个区块的基础费用。

### 简介

``cast base-fee`` [*options*] *block*

### 描述

获取一个区块的基础费用。

指定的 *block* 可以是块编号，也可以是以下任何标签之一：`earliest`、`finalized`、`safe`、`latest` 或 `pending`。默认为 `latest`。

### 可选

#### 查询选项


#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 例子

1. 获取最新区块的基础费用：
    ```sh
    cast base-fee
    ```

2. 获取创世区块的基础费用：
    ```sh
    cast base-fee 1
    ```

### 请参阅

[cast](./cast.md), [cast block](./cast-block.md), [cast age](./cast-age.md)
