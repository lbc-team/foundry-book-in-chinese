## `startBroadcast`

### 签名

```solidity
function startBroadcast() external;
```

```solidity
function startBroadcast(address who) external;
```

```solidity
function startBroadcast(uint256 privateKey) external;
```

### 描述

使用调用测试合约的地址或提供的地址/私钥作为发送方，使得所有后续调用（仅在此调用深度，并排除作弊码调用）创建可以后续在链上签名并发送的交易。

### 例子

```solidity
function t(uint256 a) public returns (uint256) {
    uint256 b = 0;
    emit log_string("here");
    return b;
}

function deployOther() public {
    vm.startBroadcast(ACCOUNT_A);
    Test test = new Test();
    
    // will trigger a transaction
    test.t(1);
    
    vm.stopBroadcast();

    // broadcast again, this time using a private key from your environment variables
    vm.startBroadcast(vm.envUint("PRIVATE_KEY"));
    test.t(3);
    vm.stopBroadcast();
}
```

### 参见

- [broadcast](./broadcast.md)
- [stopBroadcast](./stop-broadcast.md) 