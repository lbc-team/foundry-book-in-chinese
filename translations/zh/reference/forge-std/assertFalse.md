## `assertFalse`

### 签名

```solidity
function assertFalse(bool data) internal;
```

```solidity
function assertFalse(bool data, string memory err) internal;
```

### 描述

断言 `data` 为假。

可选地在回退字符串中包含错误消息。

### 例子

```solidity
bool failure = contract.fun();
assertFalse(failure);
```


### 参见

- [`assertTrue`](./assertTrue.md)