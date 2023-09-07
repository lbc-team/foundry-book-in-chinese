## `accesses`

### 签名

```solidity
function accesses(
  address
)
external
returns (
  bytes32[] memory reads,
  bytes32[] memory writes
);
```

### 描述

获取所有给定地址 storage slots 所有的被 读(`reads`) 或 写(`writes`) 的情况。

注意需要之前调用过 [`record`](./record.md)。

> ℹ️ **注意**
>
> 每次写操作都被被记录为额外的读操作

### 示例

```solidity
/// contract NumsContract {
///     uint256 public num1 = 100; // slot 0
///     uint256 public num2 = 200; // slot 1
/// }

vm.record();
numsContract.num2();
(bytes32[] memory reads, bytes32[] memory writes) = vm.accesses(
  address(numsContract)
);
emit log_uint(uint256(reads[0])); // 1
```
