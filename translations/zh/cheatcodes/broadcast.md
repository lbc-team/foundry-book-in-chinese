## `broadcast`

### 签名

```solidity
function broadcast() external;
```

```solidity
function broadcast(address who) external;
```

```solidity
function broadcast(uint256 privateKey) external;
```

### 描述

使用调用测试合约的地址或提供的地址/私钥作为发送方，使得下一个调用（仅在此调用深度）创建一个可以稍后签名并发送到链上的交易。

### 示例

```solidity
function deploy() public {
    cheats.broadcast(ACCOUNT_A);
    Test test = new Test();

    // this won't generate tx to sign
    uint256 b = test.t(4);

    // this will
    cheats.broadcast(ACCOUNT_B);
    test.t(2);

    // this also will, using a private key from your environment variables
    cheats.broadcast(vm.envUint("PRIVATE_KEY"));
    test.t(3);
} 
```

### 另请参阅

- [startBroadcast](./start-broadcast.md)
- [stopBroadcast](./stop-broadcast.md) 