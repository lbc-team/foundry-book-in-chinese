## `rememberKey`

### 签名

```solidity
function rememberKey (uint256 privateKey) external returns (address);
```

### 描述

在 Forge 的本地钱包中存储私钥，并返回相应的地址，以便以后用于 [广播](./broadcast.md) 。

### 例子

从测试助记词路径 `m/44'/60'/0'/0/0` 派生私钥，在 Forge 的钱包中记住它，并用它开始广播交易：

```solidity
string memory mnemonic = "test test test test test test test test test test test junk";
uint256 privateKey = vm.deriveKey(mnemonic, 0);
address deployer = vm.rememberKey(privateKey);

vm.startBroadcast(deployer);
...
vm.stopBroadcast();
```

从 `PRIVATE_KEY` 环境变量中加载私钥，并用它开始广播交易：

```solidity
address deployer = vm.rememberKey(vm.envUint("PRIVATE_KEY"));

vm.startBroadcast(deployer);
...
vm.stopBroadcast();
```

### 另请参阅

- [deriveKey](./derive-key.md)

Forge 标准库:
- [deriveRememberKey](../reference/forge-std/derive-remember-key.md) 
