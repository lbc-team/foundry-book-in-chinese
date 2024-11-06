## cast logs

### 名称

cast logs - 通过签名或 topic 获取日志。

### 概要

``cast logs`` [*选项*] *sig_or_topic* [*topics_or_args...*]

### 描述

通过签名或 topic 获取日志。

(*sig_or_topic*) 可以是事件签名，也可以是其哈希 topic（位于 topics[0]）。

如果使用签名，则剩余参数必须以它们的普通形式出现。如果使用 topic，则参数必须与它们自身出现的 topic 一样。

### 选项

### 查询选项

`--from-block` *from_block*
&nbsp;&nbsp;&nbsp;&nbsp;要开始查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;也可以是标签：`earliest`、`finalized`、`safe`、`latest`或`pending`。

`--to-block` *to_block*
&nbsp;&nbsp;&nbsp;&nbsp;要停止查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;也可以是标签：`earliest`、`finalized`、`safe`、`latest`或`pending`。

`--address` *address*
&nbsp;&nbsp;&nbsp;&nbsp;要筛选的合约地址

{{#include ../common/wallet-options.md}}

{{#include ../common/rpc-options.md}}

{{#include ../common/etherscan-options.md}}

### 示例

1. 使用签名获取日志：
    ```sh
    cast logs --from-block 15537393 --to-block latest 'Transfer (address indexed from, address indexed to, uint256 value)' --address 0x2e8ABfE042886E4938201101A63730D04F160A82
    ```
2. 使用 topic 获取日志：
    ```sh
    cast logs --from-block 15537393 --to-block latest 0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef --address 0x0000000000000000000000002e8abfe042886e4938201101a63730d04f160a82
    ```

### 参见

[cast](./cast.md) 