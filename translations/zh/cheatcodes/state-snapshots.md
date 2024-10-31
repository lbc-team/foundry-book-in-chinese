## `snapshotState` 作弊码

### 签名

```solidity
/// Snapshot the current state of the evm.
/// Returns the ID of the snapshot that was created.
/// To revert a snapshot use `revertToState`.
function snapshotState() external returns (uint256 snapshotId);

/// Revert the state of the EVM to a previous snapshot
/// Takes the snapshot ID to revert to.
/// Returns `true` if the snapshot was successfully reverted.
/// Returns `false` if the snapshot does not exist.
/// **Note:** This does not automatically delete the snapshot. To delete the snapshot use `deleteStateSnapshot`.
function revertToState(uint256 snapshotId) external returns (bool success);

/// Revert the state of the EVM to a previous snapshot and automatically deletes the snapshots
/// Takes the snapshot ID to revert to.
/// Returns `true` if the snapshot was successfully reverted and deleted.
/// Returns `false` if the snapshot does not exist.
function revertToStateAndDelete(uint256 snapshotId) external returns (bool success);

/// Removes the snapshot with the given ID created by `snapshot`.
/// Takes the snapshot ID to delete.
/// Returns `true` if the snapshot was successfully deleted.
/// Returns `false` if the snapshot does not exist.
function deleteStateSnapshot(uint256 snapshotId) external returns (bool success);

/// Removes _all_ snapshots previously created by `snapshot`.
function deleteStateSnapshots() external;
```

### 描述

`snapshotState` 获取区块链状态的快照并返回创建的快照标识符。

`revertToState` 将区块链的状态恢复到给定的状态快照。这将删除给定的快照，以及在其后拍摄的任何快照（例如：恢复到 id 2 将删除 id 为 2、3、4 等的快照）。

### 示例

```solidity
struct Storage {
    uint slot0;
    uint slot1;
}

contract SnapshotStateTest is Test {
    Storage store;
    uint256 timestamp;

    function setUp() public {
        store.slot0 = 10;
        store.slot1 = 20;
        vm.deal(address(this), 5 ether);            // balance = 5 ether
        timestamp = block.timestamp;
    }

    function testSnapshotState() public {
        uint256 snapshot = vm.snapshotState();      // saves the state

        // let's change the state
        store.slot0 = 300;
        store.slot1 = 400;
        vm.deal(address(this), 500 ether);
        vm.warp(12345);                             // block.timestamp = 12345

        assertEq(store.slot0, 300);
        assertEq(store.slot1, 400);
        assertEq(address(this).balance, 500 ether);
        assertEq(block.timestamp, 12345);

        vm.revertToState(snapshot);                 // restores the state

        assertEq(store.slot0, 10, "snapshot revert for slot 0 unsuccessful");
        assertEq(store.slot1, 20, "snapshot revert for slot 1 unsuccessful");
        assertEq(address(this).balance, 5 ether, "snapshot revert for balance unsuccessful");
        assertEq(block.timestamp, timestamp, "snapshot revert for timestamp unsuccessful");
    }
}
```