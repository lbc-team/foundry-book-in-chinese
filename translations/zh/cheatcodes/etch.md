## `etch`

### 签名

```solidity
function etch(address who, bytes calldata code) external;
```

### 描述

将地址 `who` 的字节码设置为 `code`。

### 例子

```solidity
bytes memory code = address(awesomeContract).code;
address targetAddr = makeAddr("target");
vm.etch(targetAddr, code);
log_bytes(address(targetAddr).code); // 0x6080604052348015610010...
```

#### 使用 `vm.etch` 启用自定义预编译

一些链，如 Blast 或 Arbitrum，运行时使用自定义预编译。Foundry 在原生 EVM 上运行，并不了解这些预编译。如果你遇到由于预编译不可用而导致的回滚，可以使用 `vm.etch` 作弊码将缺失的预编译模拟注入到预期出现的地址。

```solidity
{{#include ../../projects/cheatcodes/test/BlastMock.t.sol:all}}
```

<div class="warning">

注入预编译的模拟可能会很棘手，因为这样的模拟不会完全模拟链上实际预编译的行为。

在上述情况下，如果配置了任何收益模式，模拟将不会导致实际收益的累积。

</div>


### 另请参阅

Forge 标准库

- [`deployCode`](../reference/forge-std/deployCode.md) 
- [`deployCodeTo`](../reference/forge-std/deployCodeTo.md) 