## cast publish

### 名称

cast-publish - 向网络发布一个原始交易。

### 简介

``cast publish`` [*options*] *tx*

### 描述

向网络发布一个原始交易。

### 可选

#### Publish Options

`--async`  
`--cast-async`  
&nbsp;&nbsp;&nbsp;&nbsp;不要等待交易收据。 
&nbsp;&nbsp;&nbsp;&nbsp;环境：`CAST_ASYNC`

{{#include ../common/rpc-options.md}}

{{#include common-options.md}}

### 例子

1. 发布一个预先签署的交易：
    ```sh
    cast publish --rpc-url $RPC $TX
    ```

2. 用flashbots发布一个预签名的交易：
    ```sh
    cast publish --flashbots $TX
    ```

### 请参阅

[cast](./cast.md), [cast call](./cast-call.md), [cast send](./cast-send.md), [cast receipt](./cast-receipt.md), [cast mktx](./cast-mktx.md)