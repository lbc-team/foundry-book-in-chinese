## cast rpc

### 名称

cast-rpc -  执行一个原始的 JSON-RPC 请求

### 简介

``cast rpc`` [*options*] *METHOD* [*PARAMS...*]

### 描述

为给定的函数和参数执行一个简单的 JSON-RPC POST 请求。

### 可选

#### 查询选项

`-r` *url*  
`--rpc-url` *url*  
&nbsp;&nbsp;&nbsp;&nbsp;provider 的 URL

`-w`  
`--raw`  
&nbsp;&nbsp;&nbsp;&nbsp;按原样传递 "params"。
&nbsp;&nbsp;&nbsp;&nbsp; 如果 --raw 被传递，第一个 PARAM 将被当作 "params" 的值。 如果没有给出 params，将使用stdin。比如说：
&nbsp;&nbsp;&nbsp;&nbsp; rpc eth_getBlockByNumber '["0x123", false]' --raw
&nbsp;&nbsp;&nbsp;&nbsp;   => {"method": "eth_getBlockByNumber", "params": ["0x123", false] ... }

### 例子

1. 在 localhost 获取最新的 `eth_getBlockByNumber`：

    ```sh
    cast rpc eth_getBlockByNumber "latest" "false"
    ```

2. 在 localhost 获取 `eth_getTransactionByHash`：

    ```sh
    cast rpc eth_getTransactionByHash 0x2642e960d3150244e298d52b5b0f024782253e6d0b2c9a01dd4858f7b4665a3f
    ```

3. 在以太坊主网获取最新的 `eth_getBlockByNumber`:

   ```sh
   cast rpc --rpc-url https://mainnet.infura.io/v3/ eth_getBlockByNumber "latest" "false"
   ```