## cast etherscan-source

### 名称

cast-etherscan-source - 从 Etherscan 获取合约的源代码。

### 简介

``cast etherscan-source`` [*options*] *address*

### 描述

从 Etherscan 获取合约的源代码。

目的地（*to*）可以是 ENS 名称或地址。

### 可选

#### 输出选项

`-d` *directory*  
&nbsp;&nbsp;&nbsp;&nbsp;输出目录，将源码输出到其中。
&nbsp;&nbsp;&nbsp;&nbsp;如果不提供，源码将被输出到 stdout。

`-f`  
`--flatten`  
&nbsp;&nbsp;&nbsp;&nbsp;是否展开源代码。

{{#include ../common/etherscan-options.md}}

{{#include common-options.md}}

### 例子

1. 获取 WETH 合约的源代码：
    ```sh
    cast etherscan-source 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

2. 将 WETH 合约的源代码输出到一个名为 `weth` 的目录中。
    ```sh
    cast etherscan-source -d weth 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

### 请参阅

[cast](./cast.md)
