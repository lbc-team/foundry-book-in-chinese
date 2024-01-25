## 静态分析器

### Slither

要使用 [slither](https://github.com/crytic/slither) 测试您的项目，这里有一个示例 `slither.config.json`：

```json
{
  "filter_paths": "lib"
}
```

要在整个项目上运行 Slither，请在项目的根目录中使用以下命令：

```sh
slither .
```

默认情况下（截至版本 0.10.0），这将跳过测试和脚本。要强制包含测试和脚本，请添加 `--foundry-compile-all` 标志。

要在单个文件上运行 Slither，请使用以下命令：

```sh
slither src/Contract.sol
```

请注意，这需要在 [foundry 配置文件中配置 solc 版本](https://book.getfoundry.sh/reference/config/solidity-compiler#solc_version)。

您无需通过 `solc_remaps` 选项提供重映射，因为 Slither 将自动检测 Foundry 项目中的重映射。Slither 将调用 `forge` 来执行构建。

有关更多信息，请参阅 [Slither wiki](https://github.com/crytic/slither/wiki/Usage)。

为了使用自定义配置，例如上面提到的示例 `slither.config.json`，可以使用以下命令，如 [slither-wiki](https://github.com/crytic/slither/wiki/Usage#configuration-file) 中所述。默认情况下，slither 会查找 `slither.config.json`，但您可以定义路径和您选择的任何其他 `json` 文件：

```sh
slither --config-file <path>/file.config.json .
```

示例输出（Raw）：

```bash 
Pragma version^0.8.13 (Counter.sol#2) necessitates a version too recent to be trusted. Consider deploying with 0.6.12/0.7.6/0.8.7
solc-0.8.13 is not recommended for deployment
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-versions-of-solidity

setNumber(uint256) should be declared external:
        - Counter.setNumber(uint256) (Counter.sol#7-9)
increment() should be declared external:
        - Counter.increment() (Counter.sol#11-13)
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#public-function-that-could-be-declared-external
Counter.sol analyzed (1 contracts with 78 detectors), 4 result(s) found
```

Slither 还有一个用于 CI/CD 的 [GitHub Action](https://github.com/marketplace/actions/slither-action)。

### Mythril

要使用 [mythril](https://github.com/ConsenSys/mythril) 测试您的项目，这里有一个示例 `mythril.config.json`：

```json
{
  "remappings": [
    "ds-test/=lib/ds-test/src/",
    "forge-std/=lib/forge-std/src/"
  ],
  "optimizer": {
    "enabled": true,
    "runs": 200
  }
}
```

请注意，您需要将 `rust` 切换为 nightly 版本以安装 `mythril`：

```ignore
rustup default nightly
pip3 install mythril
myth analyze src/Contract.sol --solc-json mythril.config.json
```

有关详细信息，请参阅 [mythril 文档](https://mythril-classic.readthedocs.io/en/develop/)。

您可以使用 `--solc-json` 标志将自定义 Solc 编译器输出传递给 Mythril。 例如：

```bash
$ myth analyze src/Counter.sol --solc-json mythril.config.json
.
.
mythril.laser.plugin.loader [INFO]: Loading laser plugin: coverage
mythril.laser.plugin.loader [INFO]: Loading laser plugin: mutation-pruner
.
.
Achieved 11.56% coverage for code: 608060405234801561001057600080fd5b5060f78061001f6000396000f3fe6080604052348015600f57600080fd5b5060043610603c5760003560e01c80633fb5c1cb1460415780638381f58a146053578063d09de08a14606d575b600080fd5b6051604c3660046083565b600055565b005b605b60005481565b60405190815260200160405180910390f35b6051600080549080607c83609b565b9190505550565b600060208284031215609457600080fd5b5035919050565b60006001820160ba57634e487b7160e01b600052601160045260246000fd5b506001019056fea2646970667358221220659fce8aadca285da9206b61f95de294d3958c409cc3011ded856f421885867464736f6c63430008100033
mythril.laser.plugin.plugins.coverage.coverage_plugin [INFO]: Achieved 90.13% coverage for code: 6080604052348015600f57600080fd5b5060043610603c5760003560e01c80633fb5c1cb1460415780638381f58a146053578063d09de08a14606d575b600080fd5b6051604c3660046083565b600055565b005b605b60005481565b60405190815260200160405180910390f35b6051600080549080607c83609b565b9190505550565b600060208284031215609457600080fd5b5035919050565b60006001820160ba57634e487b7160e01b600052601160045260246000fd5b506001019056fea2646970667358221220659fce8aadca285da9206b61f95de294d3958c409cc3011ded856f421885867464736f6c63430008100033
mythril.laser.plugin.plugins.instruction_profiler [INFO]: Total: 1.0892839431762695 s
[ADD         ]   0.9974 %,  nr      9,  total   0.0109 s,  avg   0.0012 s,  min   0.0011 s,  max   0.0013 s
.
.
[SWAP1       ]   1.8446 %,  nr     18,  total   0.0201 s,  avg   0.0011 s,  min   0.0010 s,  max   0.0013 s
[SWAP2       ]   0.8858 %,  nr      9,  total   0.0096 s,  avg   0.0011 s,  min   0.0010 s,  max   0.0011 s

mythril.analysis.security [INFO]: Starting analysis
mythril.mythril.mythril_analyzer [INFO]: Solver statistics: 
Query count: 61 
Solver time: 3.6820807456970215
The analysis was completed successfully. No issues were detected.
```


结果将列在输出的末尾（如果有的话）。 由于默认的 `Counter.sol` 没有任何逻辑，`mythx` 报告未发现任何问题。
