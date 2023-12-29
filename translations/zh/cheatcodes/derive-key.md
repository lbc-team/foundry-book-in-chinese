## `deriveKey`

### 签名

```solidity
function deriveKey(
  string calldata mnemonic,
  uint32 index
) external returns (uint256);
```

```solidity
function deriveKey(
  string calldata mnemonic,
  string calldata path,
  uint32 index
) external returns (uint256);
```

### 描述

从给定的助记词或助记词文件路径派生私钥。

第一个签名派生到派生路径 `m/44'/60'/0'/0/{index}`。
第二个签名允许您将派生路径指定为第二个参数。

### 例子

从测试助记词的路径 `m/44'/60'/0'/0/0` 派生私钥：

```solidity
string memory mnemonic = "test test test test test test test test test test test junk";
uint256 privateKey = vm.deriveKey(mnemonic, 0);
```

从测试助记词的路径 `m/44'/60'/0'/1/0` 派生私钥：

```solidity
string memory mnemonic = "test test test test test test test test test test test junk";
uint256 privateKey = vm.deriveKey(mnemonic, "m/44'/60'/0'/1/", 0);
```

### 另请参阅

- [rememberKey](./remember-key.md)

Forge 标准库:
- [deriveRememberKey](../reference/forge-std/derive-remember-key.md)