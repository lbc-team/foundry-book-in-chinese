## `assertTrue`

### 签名

```solidity
function assertTrue(bool data) internal;
```

```solidity
function assertTrue(bool data, string memory err) internal;
```

### 描述

断言 `data` 为真。

可选地在回退字符串中包含错误消息。

### 示例

```solidity
bool success = contract.fun();
assertTrue(success);
```

### 参见

- [`assertFalse`](./assertFalse.md)