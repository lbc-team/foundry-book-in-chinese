## `makePersistent`

### 签名

```solidity
function makePersistent(address account) external;
function makePersistent(address account0, address account1) external;
function makePersistent(address account0, address account1, address account2) external;
function makePersistent(address[] calldata accounts) external;
```

### 描述

每个分叉（[`createFork`](./create-fork.md)）都有其独立的存储空间，当选择另一个分叉时（[`selectFork`](./select-fork.md)），存储空间也会被替换。
默认情况下，只有测试合约账户和调用者在不同分叉之间是持久的，这意味着在选择不同分叉时，测试合约的状态（变量）的更改会被保留。这样一来，数据可以通过将其存储在合约的变量中来共享。

然而，使用此作弊码，可以将指定的账户标记为持久的，这意味着它们的状态在当前活动的分叉是什么并不重要。

### 例子

将新合约标记为持久的

```solidity
contract SimpleStorageContract {
    string public value;

    function set(uint256 _value) public {
        value = _value;
    }
}

function testMarkPersistent() public {
    // by default the `sender` and the contract itself are persistent
    assert(cheats.isPersistent(msg.sender));
    assert(cheats.isPersistent(address(this)));

    // select a specific fork
    cheats.selectFork(mainnetFork);
    
    // create a new contract that's stored in the `mainnetFork` storage
    SimpleStorageContract simple = new SimpleStorageContract();
    
    // `simple` is not marked as persistent
    assert(!cheats.isPersistent(address(simple)));
    
    // contract can be used
    uint256 expectedValue = 99;
    simple.set(expectedValue);
    assertEq(simple.value(), expectedValue);
    
    // mark as persistent
    cheats.makePersistent(address(simple));
    
    // select a different fork
    cheats.selectFork(optimismFork);
    
    // ensure contract is still persistent   
    assert(cheats.isPersistent(address(simple)));
    
    // value is set as expected
    assertEq(simple.value(), expectedValue);
}
```

### 另请参阅

- [isPersistent](./is-persistent.md)
- [revokePersistent](./revoke-persistent.md)
- [createFork](./create-fork.md)
- [selectFork](./select-fork.md)
