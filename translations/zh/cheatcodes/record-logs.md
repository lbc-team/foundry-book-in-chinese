## `recordLogs`

### 签名

```solidity
function recordLogs() external;
```

### 描述

告诉虚拟机开始记录所有已发出的事件。要访问它们，请使用 [`getRecordedLogs`](./get-recorded-logs.md)。

### 例子

```solidity
/// event LogCompleted(
///   uint256 indexed topic1,
///   bytes data
/// );

vm.recordLogs();

emit LogCompleted(10, "operation completed");

Vm.Log[] memory entries = vm.getRecordedLogs();

assertEq(entries.length, 1);
assertEq(entries[0].topics[0], keccak256("LogCompleted(uint256,bytes)"));
assertEq(entries[0].topics[1], bytes32(uint256(10)));
assertEq(abi.decode(entries[0].data, (string)), "operation completed");
```
