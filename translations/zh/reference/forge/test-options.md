#### Test Options

`-m` *regex*  
`--match` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定正则匹配的测试函数。
&nbsp;&nbsp;&nbsp;&nbsp;**已废弃: 请见 `--match-test`.**

`--match-test` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定正则匹配的测试函数。

`--no-match-test` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定正则不匹配的测试函数。

`--match-contract` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定正则匹配的测试合约。

`--no-match-contract` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定正则不匹配的测试合约。

`--match-path` *glob*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定 glob 匹配的测试源文件。

`--no-match-path` *glob*  
&nbsp;&nbsp;&nbsp;&nbsp;只运行与指定 glob 不匹配的测试源文件。

`--debug` *regex*  
&nbsp;&nbsp;&nbsp;&nbsp;在调试器中运行一个测试。

&nbsp;&nbsp;&nbsp;&nbsp;传递给这个标志的参数是你想要运行的测试函数的名称，它的作用与 `--match-test` 相同。
&nbsp;&nbsp;&nbsp;&nbsp;如果符合你指定标准的测试超过一个，你必须添加额外的过滤器，直到只找到一个测试（见 `--match-contract` 和 `-match-path`）。

&nbsp;&nbsp;&nbsp;&nbsp;无论测试的结果如何，匹配的测试将在调试器中打开。

&nbsp;&nbsp;&nbsp;&nbsp;如果匹配的测试是一个模糊测试，那么它将在第一个失败用例上打开调试器。如果模糊测试没有失败，它将在最后一个模糊用例下打开调试器。

&nbsp;&nbsp;&nbsp;&nbsp;为了更精细地控制运行哪种模糊用例，请参阅 [`forge debug`](./forge-debug.md) 。

`--gas-report`  
&nbsp;&nbsp;&nbsp;&nbsp;打印一个 Gas 报告。

`--allow-failure`  
&nbsp;&nbsp;&nbsp;&nbsp;即使测试失败，也以代码 0 退出。

`--fail-fast`  
&nbsp;&nbsp;&nbsp;&nbsp;在第一次测试失败时终止测试。

`--etherscan-api-key` *key*  
&nbsp;&nbsp;&nbsp;&nbsp;Etherscan 的 API 密钥。如果被设置了，同时 `--fork-url` 也被设置了，就会使用 Etherscan 解码追踪。
&nbsp;&nbsp;&nbsp;&nbsp;环境变量：`ETHERSCAN_API_KEY`

