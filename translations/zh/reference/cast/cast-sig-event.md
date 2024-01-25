## cast sig-event

### 名称

cast-sig-event - 从事件字符串生成事件签名。

### 概要

``cast sig-event`` [*options*] *event_string*

### 描述

从事件字符串生成事件签名。

### 选项

{{#include common-options.md}}

### 示例

1. 获取日志 `Transfer(address indexed from, address indexed to, uint256 amount)` 的哈希值：
    ```sh
    cast sig-event "Transfer(address indexed from, address indexed to, uint256 amount)"
    ```

### 另请参阅

[cast](./cast.md) 