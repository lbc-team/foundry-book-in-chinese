## `txGasPrice`

### 签名

```solidity
function txGasPrice(uint256) external;
```

```solidity
function txGasPrice(uint256 newGasPrice) external;
```

### 描述

设置 `tx.gasprice` **用于交易的其余部分**。

### 例子

我们可以使用这个来获取交易的准确 gas 使用量。

```solidity
function testCalculateGas() public {
    vm.txGasPrice(2);
    uint256 gasStart = gasleft();
    myContract.doStuff();
    uint256 gasEnd = gasleft();
    uint256 gasUsed = (gasStart - gasEnd) * tx.gasprice; // tx.gasprice is now 2
}
```