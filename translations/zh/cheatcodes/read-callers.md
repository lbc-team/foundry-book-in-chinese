## `readCallers`

### 签名

```solidity
enum CallerMode {
    None,
    Broadcast,
    RecurrentBroadcast,
    Prank,
    RecurrentPrank
}

function readCallers() 
external 
returns (CallerMode callerMode, address msgSender, address txOrigin);
```

### 描述

读取当前的`CallerMode`，`msg.sender`和`tx.origin`。

`CallerMode`枚举指示是否存在活动的调用者修改以及其类型。

- 如果存在活动的 prank：
  - 如果使用 [`prank`](./prank.md) 设置了 prank：
    - `callerMode`将等于`CallerMode.Prank`。
  - 如果使用 [`startPrank`](./start-prank.md) 设置了 prank：
    - `callerMode`将等于`CallerMode.RecurrentPrank`。

- 如果存在活动的广播：
  - 如果使用 [`broadcast`](./broadcast.md) 设置了广播：
    - `callerMode`将等于`CallerMode.Broadcast`。
  - 如果使用 [`startBroadcast`](./start-broadcast.md) 设置了广播：
    - `callerMode`将等于`CallerMode.RecurrentBroadcast`。

- 如果没有活动的调用者修改：
  - `callerMode`将等于`CallerMode.None`。

### 示例

```solidity
CallerMode callerMode;
address msgSender;
address txOrigin;

// Example 1
(callerMode, msgSender, txOrigin) = vm.readCallers();
assertEq(callerMode, CallerMode.None);
assertEq(msgSender, defaultSenderAddress);
assertEq(txOrigin, defaultOriginAddress);

// Example 2
vm.prank(senderPrankAddress);
(callerMode, msgSender, txOrigin) = vm.readCallers();
assertEq(callerMode, CallerMode.Prank);
assertEq(msgSender, senderPrankAddress);
assertEq(txOrigin, defaultOriginAddress);

// Example 3
vm.prank(senderPrankAddress, originPrankAddress);
(callerMode, msgSender, txOrigin) = vm.readCallers();
assertEq(callerMode, CallerMode.Prank);
assertEq(msgSender, senderPrankAddress);
assertEq(txOrigin, originPrankAddress);

// Example 4
vm.broadcast(broadcastAddress);
(callerMode, msgSender, txOrigin) = vm.readCallers();
assertEq(callerMode, CallerMode.Broadcast);
assertEq(msgSender, broadcastAddress);
assertEq(txOrigin, broadcastAddress);
```

### 另请参阅

- [prank](./prank.md)
- [startPrank](./start-prank.md)
- [broadcast](./broadcast.md)
- [startBroadcast](./start-broadcast.md)