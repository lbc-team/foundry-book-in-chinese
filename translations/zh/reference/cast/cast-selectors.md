## cast 选择器

### 名称

cast-selectors - 从字节码中提取函数选择器和参数

### 概述

``cast selectors`` [*选项*] *字节码*

### 描述

使用 [EVMole library](https://github.com/cdump/evmole) 从字节码中提取函数选择器和参数

### 选项

`-r`  
`--resolve`  
&nbsp;&nbsp;&nbsp;&nbsp;使用 https://openchain.xyz 解析提取的选择器的函数签名

{{#include common-options.md}}

### 示例

1. 获取 WETH 的合约函数签名和参数：
    ```sh
    cast selectors $(cast code 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2)
    ```

### 另见

[cast](./cast.md), [cast 4byte](./cast-4byte.md)