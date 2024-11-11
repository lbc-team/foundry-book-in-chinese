## `changePrank`

### 状态

`changePrank` 已废弃.

### 签名

```solidity
function changePrank(address who) internal;
```

### 描述

用 `stopPrank` 停止正在进行的 prank，并将地址传给  `startPrank`。

对于在 `setUp` 函数中启动全局 prank 和在某些测试中停用它很有用。