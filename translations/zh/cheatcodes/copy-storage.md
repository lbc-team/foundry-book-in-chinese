## `copyStorage`

### 签名

```solidity
function copyStorage(address from, address to) external;
```

### 描述

实用的作弊代码，用于将 `from` 合约的存储复制到另一个 `to` 合约。
如果目标地址设置了任意存储，则不允许使用作弊代码。

### 示例

给定一个合约
```solidity
contract Counter {
    uint256 public count;

    function setCount(uint256 x) public {
        count = x;
    }
}
```
使用 `copyStorage` 作弊代码将一个实例上的存储复制到另一个地址：
```solidity
function testCopyStorage() public {
    Counter original = new Counter();
    original.setCount(1000);
    Counter copy = new Counter();
    copy.setCount(1);
    // Check initial count on copy.
    assertEq(copy.count(), 1);

    vm.copyStorage(address(original), address(copy));
    // Value is copied from first contract to copy.
    assertEq(copy.count(), 1000);
}
```