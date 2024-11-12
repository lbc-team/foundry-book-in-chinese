## forge inspect

### 名称

forge-inspect - 获得关于智能合约的专门信息

### 简介

``forge inspect`` [*options*] *contract_name* *field*

### 描述

获得关于智能合约的专门信息

要检查的字段（*字段*）可以是以下任何一种。

- `abi`
- `b`/`bytes`/`bytecode`
- `deployedBytecode`/`deployed_bytecode`/`deployed-bytecode`/`deployedbytecode`/`deployed`
- `assembly`/`asm`
- `asmOptimized`/`assemblyOptimized`/`assemblyoptimized`/`assembly_optimized`/`asmopt`/`assembly-optimized`/`asmo`/`asm-optimized`/`asmoptimized`/`asm_optimized`
- `methods`/`methodidentifiers`/`methodIdentifiers`/`method_identifiers`/`method-identifiers`/`mi`
- `gasEstimates`/`gas`/`gas_estimates`/`gas-estimates`/`gasestimates`
- `storageLayout`/`storage_layout`/`storage-layout`/`storagelayout`/`storage`
- `devdoc`/`dev-doc`/`devDoc`
- `ir`
- `ir-optimized`/`irOptimized`/`iroptimized`/`iro`/`iropt`
- `metadata`/`meta`
- `userdoc`/`userDoc`/`user-doc`
- `ewasm`/`e-wasm`
- `errors`
- `events`

### 选项

`--pretty`  
&nbsp;&nbsp;&nbsp;&nbsp;如果支持的话，漂亮地打印所选字段。

{{#include core-build-options.md}}

{{#include common-options.md}}

### 例子

1. 检查一个合约的字节码。
    ```sh
    forge inspect MyContract bytecode
    ```

2. 检查合约的存储布局。
    ```sh
    forge inspect MyContract storage
    ```

3 . 以漂亮的格式检查合约的 abi：
   ```sh
   forge inspect --pretty MyContract abi
   ```

### 另请参阅

[forge](./forge.md), [forge build](./forge-build.md)
