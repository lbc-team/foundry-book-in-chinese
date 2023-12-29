## forge script

### 名称

forge-script - 以脚本形式运行智能合约，建立可在链上发送的交易。

### 简介

``forge script`` [*options*] *path* [*args...*]

### 描述


以脚本形式运行智能合约，建立可在链上发送的交易。

脚本可用于在实时合约上应用状态转换，或使用 Solidity 部署和初始化一组复杂的智能合约。

### 选项

`--broadcast`  
&nbsp;&nbsp;&nbsp;&nbsp;广播交易。

`--debug`  
&nbsp;&nbsp;&nbsp;&nbsp;在 [调试器][debugger] 中打开脚本。优先于 broadcast。

`-g`  
`--gas-estimate-multiplier` *multiplier*  
&nbsp;&nbsp;&nbsp;&nbsp;相对百分比，所有 Gas 估计值都要乘以这个百分比。(即设置为 200 以使其加倍)
&nbsp;&nbsp;&nbsp;&nbsp;默认: 130

`--json`  
&nbsp;&nbsp;&nbsp;&nbsp;以 JSON 格式输出结果。
&nbsp;&nbsp;&nbsp;&nbsp;注意：该输出正在开发中，容易发生变化。

`--legacy`  
&nbsp;&nbsp;&nbsp;&nbsp;使用传统的交易而不是 EIP1559 交易。这对于没有 EIP1559 的普通网络来说是自动启用的。

`--resume`  
&nbsp;&nbsp;&nbsp;&nbsp;恢复并提交之前失败或超时的交易。

`-s`  
`--sig` *signature*  
&nbsp;&nbsp;&nbsp;&nbsp;你想在合约中调用的函数的签名，或原始的 calldata。 
&nbsp;&nbsp;&nbsp;&nbsp;默认：`run()`  

`--skip-simulation`  
&nbsp;&nbsp;&nbsp;&nbsp;跳过链上模拟。

`--non-interactive`  
&nbsp;&nbsp;&nbsp;&nbsp;移除交互式提示，如果合约接近 [EIP-170](https://eips.ethereum.org/EIPS/eip-170) 大小限制，则会出现交互式提示。

`--slow`  
&nbsp;&nbsp;&nbsp;&nbsp;确保只有在前一个交易得到确认和成功之后才发送交易。

`--target-contract` *contract_name*  
&nbsp;&nbsp;&nbsp;&nbsp;你想要运行的合约名称。

`--priority-gas-price`  
&nbsp;&nbsp;&nbsp;&nbsp;设置 EIP1559 交易的优先级 Gas 价格。当 Gas 价格波动较大且您希望确保您的交易被包含时，此选项非常有用。

`--with-gas-price` *price*  
&nbsp;&nbsp;&nbsp;&nbsp;设置 **广播** 传统交易的 Gas 价格，或广播 EIP1559 交易的最大费用。 
&nbsp;&nbsp;&nbsp;&nbsp;注意：要在脚本的执行环境中设置 Gas 价格，请使用 `--gas-price` 代替（见下文）。

{{#include ../common/etherscan-options.md}}

#### Verification Options

`--verify`  
&nbsp;&nbsp;&nbsp;&nbsp;如果它发现一个匹配的广播日志，它就会尝试验证在交易回执中发现的每一个合约。

{{#include ../common/verifier-options.md}}

{{#include ../common/retry-options.md}}

{{#include core-build-options.md}}

#### 构建选项

`--names`  
&nbsp;&nbsp;&nbsp;&nbsp;打印编译的合约名称。

`--sizes`  
&nbsp;&nbsp;&nbsp;&nbsp;打印已编译的非测试合约大小，如果其中任何一个超过大小限制，则以代码 1 退出。

{{#include watch-options.md}}

{{#include ../common/multi-wallet-options.md}}

{{#include evm-options.md}}

{{#include executor-options.md}}

{{#include common-options.md}}

### 例子

1. 以脚本形式运行 `BroadcastTest`，在链上广播产生的交易
    ```sh
    forge script ./test/Broadcast.t.sol --tc BroadcastTest --sig "deploy()" \
                 -vvv --fork-url $GOERLI_RPC_URL
    ```

2. 在 Polygon 上部署合约（请参阅脚本教程以获取示例脚本）。*每个网络的验证器网址都不同。*
    ```sh
    forge script script/NFT.s.sol:MyScript --chain-id 137 --rpc-url $RPC_URL \
        --etherscan-api-key $POLYGONSCAN_API_KEY --verifier-url https://api.polygonscan.com/api \
        --broadcast --verify -vvvv
    ```

3. 恢复失败的脚本。以上述为例，删除`--broadcast`，添加`--resume`
    ```sh
    forge script script/NFT.s.sol:MyScript --chain-id 137 --rpc-url $RPC_URL \
        --etherscan-api-key $POLYGONSCAN_API_KEY --verifier-url https://api.polygonscan.com/api \
        --verify -vvvv --resume
    ```

4. 验证刚刚部署的合约所使用的脚本
    ```sh
    forge script script/NFT.s.sol --rpc-url $RPC_URL --verify --resume
    ```

[debugger]: ../../forge/debugger.md
