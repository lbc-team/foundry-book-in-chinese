## `checked_write`

### 签名

```solidity
function checked_write(StdStorage storage self, address who) internal;
```

```solidity
function checked_write(StdStorage storage self, uint256 amt) internal;
```

```solidity
function checked_write(StdStorage storage self, bool write) internal;
```

```solidity
function checked_write(StdStorage storage self, bytes32 set) internal;
```

```solidity
function checked_write_int(StdStorage storage self, int256 val) internal;
```

### 描述

设置要写到存储槽的数据。

如果不成功，则 Reverts，并提供一个信息。
