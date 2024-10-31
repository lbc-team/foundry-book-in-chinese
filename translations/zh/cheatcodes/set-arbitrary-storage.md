## `setArbitraryStorage`

### 签名

```solidity
function setArbitraryStorage(address target) external;
```

### 描述

实用的作弊代码，使给定地址的存储完全符号化。
任何后续对目标存储的 `SLOAD` 操作都会读取一个任意值，如果同一槽位再次加载，则返回该值。
如果存储槽位被显式写入（在第一次加载之前或之后），则返回写入的值。

### 示例

对于具有以下存储布局的合约：
```solidity
contract Counter {
    address[] public owners;

    function getOwner(uint256 pos) public view returns (address) {
        return owners[pos];
    }

    function setOwner(uint256 pos, address owner) public {
        owners[pos] = owner;
    }
}
```
使用 `setArbitraryStorage` 作弊代码确保返回任意值：
```solidity
contract ArbitraryStorageTest is Test {
    function testArbitraryStorage() public {
        Counter counter = new Counter();
        vm.setArbitraryStorage(address(counter));
        // Next call would fail with array out of bounds without arbitrary storage
        address owner = counter.getOwner(55);
        // Subsequent calls to same slot returns same value
        assertEq(counter.getOwner(55), owner);
        // The new value is returned if explicitly written
        counter.setOwner(55, address(111));
        assertEq(counter.getOwner(55), address(111));
    }
}
```