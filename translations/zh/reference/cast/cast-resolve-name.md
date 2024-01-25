## cast resolve-name

### 名称

cast-resolve-name - 进行 ENS 查询。

### 简介

``cast resolve-name`` [*options*] *who*

### 描述

进行 ENS 查询。

如果输入了 `--verify`，那么在正常查询之后会进行反向查询，以验证名字是否正确。

### 可选

#### Lookup Options

`-v`  
`--verify`  
&nbsp;&nbsp;&nbsp;&nbsp;进行反向查询以验证该名称是否正确。

#### RPC Options

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 例子

1. 获取一个 ENS 名称的地址。
    ```sh
    cast resolve-name vitalik.eth
    ```

2. 同时进行正常查询和反向查询：
    ```sh
    cast resolve-name --verify vitalik.eth
    ```

### 请参阅

[cast](./cast.md), [cast lookup-address](./cast-lookup-address.md)
