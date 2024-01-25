## cast compute-address

### 名称

cast-compute-address - 从给定的 nonce 和部署者地址计算合约地址。

### 简介

``cast compute-address`` [*options*] *address*

### 描述

从给定的 nonce 和部署者地址计算合约地址。

### 可选

#### Compute 选项

`--nonce` *nonce*  
&nbsp;&nbsp;&nbsp;&nbsp;账户的 nonce。 默认为从 RPC 获取最新的 nonce。

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 请参阅

[cast](./cast.md), [cast proof](./cast-proof.md), [cast create2](./cast-create2.md)
