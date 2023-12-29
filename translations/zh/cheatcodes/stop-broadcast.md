## `stopBroadcast`

### 签名
```solidity
function stopBroadcast() external;
```

### 描述

停止收集交易以便稍后在链上广播。

### 例子

```solidity
function deployNoArgs() public {
    // broadcast the next call
    cheats.broadcast();
    Test test1 = new Test();

    // broadcast all calls between this line and `stopBroadcast`
    cheats.startBroadcast();
    Test test2 = new Test();
    cheats.stopBroadcast();
}
```