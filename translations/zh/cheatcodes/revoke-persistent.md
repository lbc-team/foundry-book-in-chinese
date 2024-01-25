## `revokePersistent`

### 签名

```solidity
    function revokePersistent(address) external;
    function revokePersistent(address[] calldata) external;
```

### 描述

[`makePersistent`](./make-persistent.md)的对应函数，使给定的合约在分叉交换中不再持久化

### 例子

撤销合约的持久状态

```solidity
contract SimpleStorageContract {
    string public value;

    function set(uint256 _value) public {
        value = _value;
    }
}

function testRevokePersistent() public {
    // select a specific fork
    cheats.selectFork(mainnetFork);
    
    // create a new contract that's stored in the `mainnetFork` storage
    SimpleStorageContract simple = new SimpleStorageContract();
    
    // `simple` is not marked as persistent
    assert(!cheats.isPersistent(address(simple)));
       
    // make it persistent
    cheats.makePersistent(address(simple));
    
    // ensure it is persistent
    assert(cheats.isPersistent(address(simple)));
    
    // revoke it
    cheats.revokePersistent(address(simple));
    
    // contract no longer persistent
    assert(!cheats.isPersistent(address(simple)));
}
```

### 另请参阅

- [isPersistent](./is-persistent.md)
- [ revokePersistent](./revoke-persistent.md)
- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)