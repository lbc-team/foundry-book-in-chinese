## `addr`

### 签名

```solidity
function addr(uint256 privateKey) external returns (address);
```

### 描述

计算给定私钥所对应的地址

### 示例

```solidity
address alice = vm.addr(1);
emit log_address(alice); // 0x7e5f4552091a69125d5dfcb7b8c2659029395bdf
```
