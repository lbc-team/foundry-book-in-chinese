## snapshot 作弊码

### 签名

```solidity
// Snapshot the current state of the evm.
// Returns the id of the snapshot that was created.
// To revert a snapshot use `revertTo`
function snapshot() external returns(uint256);
// Revert the state of the evm to a previous snapshot
// Takes the snapshot id to revert to.
// This deletes the snapshot and all snapshots taken after the given snapshot id.
function revertTo(uint256) external returns(bool);
```

### 描述

`snapshot` 对区块链的状态进行快照，并返回创建的快照的标识符

`revertTo` 将区块链的状态回滚到给定的快照。这将删除给定的快照，以及在其之后创建的任何快照（例如：回滚到 id 2 将删除 id 2、3、4 等快照）

### 示例

```solidity
struct Storage {
    uint slot0;
    uint slot1;
}

contract SnapshotTest is Test {
    Storage store;
    uint256 timestamp;

    function setUp() public {
        store.slot0 = 10;
        store.slot1 = 20;
        vm.deal(address(this), 5 ether);        // balance = 5 ether
        timestamp = block.timestamp;
    }

    function testSnapshot() public {
        uint256 snapshot = vm.snapshot();       // saves the state

        // let's change the state
        store.slot0 = 300;
        store.slot1 = 400;
        vm.deal(address(this), 500 ether);
        vm.warp(12345);                         // block.timestamp = 12345

        assertEq(store.slot0, 300);
        assertEq(store.slot1, 400);
        assertEq(address(this).balance, 500 ether);
        assertEq(block.timestamp, 12345);

        vm.revertTo(snapshot);                  // restores the state

        assertEq(store.slot0, 10, "snapshot revert for slot 0 unsuccessful");
        assertEq(store.slot1, 20, "snapshot revert for slot 1 unsuccessful");
        assertEq(address(this).balance, 5 ether, "snapshot revert for balance unsuccessful");
        assertEq(block.timestamp, timestamp, "snapshot revert for timestamp unsuccessful");
    }
}
```