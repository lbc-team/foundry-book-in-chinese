## `snapshotGas` 作弊码

### 签名

```solidity
/// Start a snapshot capture of the current gas usage by name.
/// The group name is derived from the contract name.
function startSnapshotGas(string calldata name) external;

/// Start a snapshot capture of the current gas usage by name in a group.
function startSnapshotGas(string calldata group, string calldata name) external;

/// Stop the snapshot capture of the current gas by latest snapshot name, capturing the gas used since the start.
function stopSnapshotGas() external returns (uint256 gasUsed);

/// Stop the snapshot capture of the current gas usage by name, capturing the gas used since the start.
/// The group name is derived from the contract name.
function stopSnapshotGas(string calldata name) external returns (uint256 gasUsed);

/// Stop the snapshot capture of the current gas usage by name in a group, capturing the gas used since the start.
function stopSnapshotGas(string calldata group, string calldata name) external returns (uint256 gasUsed);

/// Snapshot capture an arbitrary numerical value by name.
/// The group name is derived from the contract name.
function snapshotValue(string calldata name, uint256 value) external;

/// Snapshot capture an arbitrary numerical value by name in a group.
function snapshotValue(string calldata group, string calldata name, uint256 value) external;

/// Snapshot capture the gas usage of the last call by name from the callee perspective.
function snapshotGasLastCall(string calldata name) external returns (uint256 gasUsed);

/// Snapshot capture the gas usage of the last call by name in a group from the callee perspective.
function snapshotGasLastCall(string calldata group, string calldata name) external returns (uint256 gasUsed);
```

### 描述

`snapshotGas*` 作弊码允许你在测试中捕获 gas 使用情况。这对于跟踪你的逻辑消耗了多少 gas 非常有用。你可以通过名称捕获最后一次调用的 gas 使用情况，通过名称捕获任意数值，或者通过名称开始和停止当前 gas 使用情况的快照捕获。

为了严格比较测试运行之间的 gas 使用情况，在运行测试之前将 `FORGE_SNAPSHOT_CHECK` 环境变量设置为 `true`。这将比较你的测试的 gas 使用情况与最后一次快照，如果 gas 使用情况发生变化则会失败。默认情况下，快照目录将在每次测试运行之前被新创建并清空其内容，以确保没有过时的数据存在。

使用 `snapshotGas*` 作弊码时创建的 `snapshots` 目录应当被纳入版本控制。这允许你跟踪 gas 使用情况随时间的变化，并在代码审查期间比较 gas 使用情况。

在运行 `forge clean` 时，`snapshots` 目录将被删除。

### 示例

捕获调用外部合约的代码段的 gas 使用情况：

```solidity
contract SnapshotGasTest is Test {
    uint256 public slot0;

    Flare public flare;

    function setUp() public {
        flare = new Flare();
    }

    function testSnapshotGas() public {
        vm.startSnapshotGas("externalA");
        flare.run(256);
        uint256 gasUsed = vm.stopSnapshotGas();
    }
}
```

捕获多个修改内部状态的代码段的 gas 使用情况：

```solidity
contract SnapshotGasTest is Test {
    uint256 public slot0;


    /// Writes to `snapshots/SnapshotGasTest.json` group with name `internalA`, `internalB`, and `internalC`.
    function testSnapshotGas() public {
        vm.startSnapshotGas("internalA");
        slot0 = 1;
        vm.stopSnapshotGas();

        vm.startSnapshotGas("internalB");
        slot0 = 2;
        vm.stopSnapshotGas();

        vm.startSnapshotGas("internalC");
        slot0 = 0;
        vm.stopSnapshotGas();
    }
}
```

捕获同时修改内部状态并调用外部合约的代码段的 gas 使用情况：

```solidity
contract SnapshotGasTest is Test {
    uint256 public slot0;
    Flare public flare;

    function setUp() public {
        flare = new Flare();
    }

    /// Writes to `snapshots/SnapshotGasTest.json` group with name `combinedA`.
    function testSnapshotGas() public {
        vm.startSnapshotGas("combinedA");
        flare.run(256);
        slot0 = 1;
        vm.stopSnapshotGas();
    }
}
```

```solidity

Capturing an arbitrary numerical value (such as the bytecode size of a contract):

```solidity
contract SnapshotGasTest is Test {
    uint256 public slot0;

    /// 写入 `snapshots/SnapshotGasTest.json` 组，名称为 `valueA`、`valueB` 和 `valueC`。
    function testSnapshotValue() public {
        uint256 a = 123;
        uint256 b = 456;
        uint256 c = 789;

        vm.snapshotValue("valueA", a);
        vm.snapshotValue("valueB", b);
        vm.snapshotValue("valueC", c);
    }
}
```

Capturing the gas usage of the last call from the callee perspective:

```solidity
contract SnapshotGasTest is Test {
    Flare public flare;

    function setUp() public {
        flare = new Flare();
    }

    /// 写入 `snapshots/SnapshotGasTest.json` 组，名称为 `lastCallA`。
    function testSnapshotGasLastCall() public {
        flare.run(1);
        vm.snapshotGasLastCall("lastCallA");
    }
}
```

对于上述每个示例，也可以使用作弊码的 `group` 变体将这些快照分组到一个自定义组中。

```solidity
contract SnapshotGasTest is Test {
    uint256 public slot0;

    /// 写入 `snapshots/CustomGroup.json` 组，名称为 `internalA`、`internalB` 和 `internalC`。
    function testSnapshotGas() public {
        vm.startSnapshotGas("CustomGroup", "internalA");
        slot0 = 1;
        vm.stopSnapshotGas();

        vm.startSnapshotGas("CustomGroup", "internalB");
        slot0 = 2;
        vm.stopSnapshotGas();

        vm.startSnapshotGas("CustomGroup", "internalC");
        slot0 = 0;
        vm.stopSnapshotGas();
    }
}
```