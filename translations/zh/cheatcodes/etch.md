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

### 另请参阅

Forge 标准库

- [`deployCode`](../reference/forge-std/deployCode.md) 
- [`deployCodeTo`](../reference/forge-std/deployCodeTo.md) 