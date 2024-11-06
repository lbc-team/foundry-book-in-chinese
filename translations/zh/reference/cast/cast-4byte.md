## cast 4byte

### 名称

cast-4byte - 从 <https://sig.eth.samczsun.com> 中获取指定选择器的函数签名。

### 简介

``cast 4byte`` [*options*] *sig*

### 描述

从 <https://sig.eth.samczsun.com> 中获取指定选择器的函数签名。

### 可选

{{#include common-options.md}}

### 例子

1. 获取选择器 `0x8cc5ce99` 的函数签名:
    ```sh
    cast 4byte 0x8cc5ce99
    ```

### 请参阅

[cast](./cast.md), [cast 4byte-decode](./cast-4byte-decode.md), [cast 4byte-event](./cast-4byte-event.md), [cast selectors](./cast-selectors.md)