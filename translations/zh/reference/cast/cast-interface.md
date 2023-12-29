## cast interface

### 名称

cast-interface - 从一个给定的 ABI 生成一个 Solidity 接口。

### 简介

``cast interface`` [*options*] *address_or_path*

### 描述

从一个给定的 ABI 生成一个 Solidity 接口。

参数（*address_or_path*）可以是包含 ABI 的文件的路径，也可以是一个地址。

如果提供了一个地址，那么接口是由账户的 ABI 生成的，该 ABI 是从 Etherscan 中获取的。

> ℹ️ **注意**
>
> 该命令目前不支持 v2 ABI 编码器。

### 可选

#### Interface 选项

`-n` *name*  
`--name` *name*  
&nbsp;&nbsp;&nbsp;&nbsp;用于生成接口的名称。默认名称是 `Interface`。

`-o` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;输出文件的路径。如果不指定，接口将被输出到 stdout。

`-p` *version*  
`--pragma` *version*  
&nbsp;&nbsp;&nbsp;&nbsp; 要在接口中使用的 Solidity 编译器版本。默认：`^0.8.10`。

`-j`  
`--json`  
&nbsp;&nbsp;&nbsp;&nbsp;输出合约 JSON 格式的 ABI.

{{#include ../common/etherscan-options.md}}

{{#include common-options.md}}

### 例子

1. 从一个文件中生成一个接口：
    ```sh
    cast interface ./path/to/abi.json
    ```

2. 使用 Etherscan 生成一个接口：
    ```sh
    cast interface -o IWETH.sol 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

3. 从一个文件中生成并命名一个接口。
    ```sh
    cast interface -n LilENS ./path/to/abi.json
    ```

4. 从 Etherscan 获取 JOSN 格式的 ABI:
    ```sh
    cast interface -o IWETH.sol -j 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

### 请参阅

[cast](./cast.md), [cast proof](./cast-proof.md)
