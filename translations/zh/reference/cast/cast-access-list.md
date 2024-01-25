## cast access-list

### 名称

cast-access-list - 为一个交易创建一个访问列表。

### 简介

``cast access-list`` [*options*] *to* *sig* [*args...*]

### 描述

为一个交易创建一个访问列表。

目的地（*to*）可以是 ENS 名称或地址。

{{#include sig-description.md}}

### 可选

#### Query Options

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;你想查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是一个区块编号，或任何一个标签: `earliest`，`finalized`，`safe`，`latest` 或者 `pending`。

{{#include ../common/wallet-options.md}}

{{#include ../common/rpc-options.md}}

{{#include ../common/etherscan-options.md}}

{{#include common-options.md}}

### 请参阅

[cast](./cast.md), [cast send](./cast-send.md), [cast publish](./cast-publish.md), [cast call](./cast-call.md)
