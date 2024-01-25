## `unixTime`

### 签名

```solidity
function unixTime() external returns (uint milliseconds);
```

### 描述

以毫秒为单位返回自 Unix 纪元以来的时间。

### 例子

```solidity
uint start = vm.unixTime();
vm.sleep(10_000); // 暂停执行 10 秒钟
uint end = vm.unixTime();
assertEq(end - start, 10_000);
```

