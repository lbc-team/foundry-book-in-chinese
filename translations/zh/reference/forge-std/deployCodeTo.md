## `deployCodeTo`

### 签名

```solidity
function deployCodeTo(string memory what, address where) internal virtual;
```

```solidity
function deployCodeTo(string memory what, bytes memory args, address where) internal virtual;
```

```solidity
function deployCodeTo(string memory what, bytes memory args, uint256 value, address where) internal virtual;
```

### 描述

通过从构件目录中获取合约的字节码，伪部署合约到任意地址。这可以用于重新创建生产环境。

calldata 参数可以是`ContractFile.sol`（如果文件名和合约名相同），`ContractFile.sol:ContractName`，或者是相对于项目根目录的构件路径。

还可以通过使用 `value` 参数传递值。如果需要在构造时发送以太币，则这是必要的。

### 示例

```solidity
deployCodeTo("MyContract.sol", abi.encode(arg1, arg2), arbitraryAddr);
```

### 另请参阅

Forge 标准库

- [`deployCode`](./deployCode.md)