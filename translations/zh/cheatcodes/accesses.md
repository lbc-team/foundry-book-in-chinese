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

获取在地址上已读取（`reads`）或已写入（`writes`）的所有存储槽。

请注意，必须首先调用 [`record`](./record.md)。

> ℹ️ **注意**
>
> 每次写入也算作额外的读取。

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