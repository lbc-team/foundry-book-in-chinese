## `load`

### 签名

```solidity
function load(address account, bytes32 slot) external returns (bytes32);
```

### 描述

从账户 `account` 的存储槽 `slot` 中加载值。

### 例子

```solidity
/// contract LeetContract {
///     uint256 private leet = 1337; // slot 0
/// }

bytes32 leet = vm.load(address(leetContract), bytes32(uint256(0)));
emit log_uint(uint256(leet)); // 1337
```

### 另请参阅

Forge 标准库

[Std Storage](../reference/forge-std/std-storage.md) 