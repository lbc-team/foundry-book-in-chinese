## `stopAndReturnStateDiff`

### 签名

```solidity
enum AccountAccessKind {
    Call,
    DelegateCall,
    CallCode,
    StaticCall,
    Create,
    SelfDestruct,
    Resume
}

struct ChainInfo {
    uint256 forkId;
    uint256 chainId;
}

struct AccountAccess {
    ChainInfo chainInfo;
    AccountAccessKind kind;
    address account;
    address accessor;
    bool initialized;
    uint256 oldBalance;
    uint256 newBalance;
    bytes deployedCode;
    uint256 value;
    bytes data;
    bool reverted;
    StorageAccess[] storageAccesses;
}

struct StorageAccess {
    address account;
    bytes32 slot;
    bool isWrite;
    bytes32 previousValue;
    bytes32 newValue;
    bool reverted;
}

function stopAndReturnStateDiff() external returns (AccountAccess[] memory accesses);
```

### 描述

在调用 [`startStateDiffRecording`](./start-state-diff-recording.md) 后检索记录的状态更改。调用此函数将消耗记录的状态差异并禁用状态差异记录。可以调用 `startStateDiffRecording` 来恢复记录。

有两种类型的状态更改记录；帐户访问和存储访问，表示为 `AccountAccess` 和 `StorageAccess`。

帐户状态更改（`AccountAccess`）记录在新的 EVM 上下文开始时；即由各种 CREATE、CALL 和 SELFDESTRUCT 操作引起。
`AccountAccess` 记录包含存储访问，表示为 `StorageAccess`，它发生在通过子调用或创建操作之前被抢占之前。

`AccountAccess` 记录的排序反映了与其关联操作的 EVM 执行顺序。每当创建或恢复 EVM 上下文时，都会创建一个 `AccountAccess`。
如果创建了子上下文，则会记录一个 `Resume` `AccountAccess`，以指示已恢复先前被抢占的 `AccountAccess`。

### `AccountAccessKind`

确定已访问的 `account` 的帐户访问类型。这通常由启动帐户执行上下文的 EVM 操作指定。
如果类型是 `Call`、`DelegateCall`、`StaticCall` 或 `CallCode`，则 `account` 是被调用者。
如果类型是 Create，则帐户是新创建的帐户。
如果类型是 SelfDestruct，则帐户是自毁接收者。
如果类型是 Resume，则帐户表示已恢复的执行上下文。

- `Call` - 调用了该帐户
- `DelegateCall` - 通过委托调用调用了该帐户
- `CallCode` - 通过调用代码调用了该帐户
- `StaticCall` - 通过静态调用调用了该帐户
- `Create` - 创建了该帐户
- `SelfDestruct` - 自毁了该帐户
- `Resume` - 表示已恢复先前被抢占的帐户访问

### `AccountAccess`

- `chainInfo` - 访问的链和分叉。
- `kind` - 帐户访问类型。这确定如何解释 `AccountAccess`。
- `account` - 被访问的帐户。它是为 `AccountAccessKind.Create` 创建的帐户。
 对于 `AccountAccessKind.SelfDestruct`，它是自毁接收者。
 对于所有其他类型的 `AccountAccessKind`，它是当前 EVM 上下文的帐户。
- `accessor` - 访问了 `account` 的对象。即帐户创建者、调用者或正在自毁的帐户。
- `initialized` - 访问之前帐户是否已初始化或为空。
如果帐户已具有代码、非零 nonce 或非零余额，则视为已初始化。
- `oldBalance`: 被访问的 `account` 的先前余额。
- `newBalance` - 被访问帐户的潜在新余额。
也就是说，所有余额更改都记录在此处，即使发生回滚也是如此。
- `deployedCode` - 在 `AccountAccessKind.Create` 情况下部署的 `account` 的代码。对于所有其他帐户访问类型，此字段为空。
- `value` - 与帐户访问一起传递的值。
- `data` - 在 `CREATE` 或 `CALL` 类型访问的情况下提供的输入数据（即 `msg.data`）。
- `reverted` - 如果此访问在当前上下文或父上下文中回滚。
- `storageAccesses` - 在帐户访问为非抢占时进行的存储访问的有序列表。

### `StorageAccess`

在 `AccountAccess` 期间进行的存储访问。`StorageAccess` 不能没有关联的 `AccountAccess` 存在。这意味着当状态差异开始在给定上下文上时，该上下文（但不是其子上下文）中进行的存储访问不会被记录。

`StorageAccess`包含以下字段：

- `account` - 访问其存储的帐户
- `slot` - 被访问的槽
- `isWrite` - 如果访问是写入
- `previousValue` - 此存储访问之前的槽值
- `newValue` - 此存储访问后的槽值
- `reverted` - 如果此访问已回滚

### 恢复的 `AccountAccess`

当子上下文返回到其父上下文时生成此类型的 `AccountAccess`。它保留与原始上下文相同的值，包括 `accessor`、`account`、`initialized`、`storageAccesses` 和 `reverted`。
以下控制流表说明了如何记录恢复的 `AccountAccess`。

| Contract A 的 `alpha()` 中的步骤 | Contract B 的 `beta()` 中的步骤 | AccountAccess 记录的状态 |
|--------------------------------|-------------------------------|------------------------------------------|
| 调用 A.alpha()                |                               | [A.call]              |
| 访问状态                |                               | [A.call[A.access]]              |
| 调用 B.beta()               | B.beta()开始               | [A.call[A.access], B.call]                  |
| （执行暂停）          | 访问状态               | [A.call[A.access], B.call[B.access]]                  |
|                             |   返回                     |                        |
| 恢复执行            | （返回到 A.alpha()）         | [A.call[A.access], B.call[B.access]]             |
| 访问状态                 |                              | [<br>&emsp;A.call[A.access], <br>&emsp;B.call[B.access], <br>&emsp;A.resume[A.access']<br>]       |


> ℹ️ **注意**
>
> 仅当在恢复上下文后发生存储访问时，才会创建恢复的 `AccountAccess`。

### 示例：记录 CREATE 操作期间的存储状态更改
```solidity
contract Contract {
    uint256 internal _reserved;
    uint256 public data;
    constructor(uint _data) payable { data = _data; }
}

vm.startStateDiffRecording();
Contract contract = new Contract{value: 1 ether}(100);
Vm.AccountAccess[] memory records = vm.stopAndReturnStateDiff();

assertEq(records.length, 1);
assertEq(records[0].kind, Vm.AccountAccessKind.Create);
assertEq(records[0].account, address(contract));
assertEq(records[0].accessor, address(this));
assertEq(records[0].initialized, true);
assertEq(records[0].oldBalance, 0);
assertEq(records[0].newBalance, 1 ether);
assertEq(records[0].deployedCode, address(contract).code);
assertEq(records[0].value, 1 ether);
assertEq(records[0].data, abi.encodePacked(type(Contract).creationCode, (uint(100))));
assertEq(records[0].reverted, false);

assertEq(records[0].storageAccesses.length, 1);
assertEq(records[0].storageAccesses[0].account, address(contract));
assertEq(records[0].storageAccesses[0].slot, bytes32(uint256(1)));
assertEq(records[0].storageAccesses[0].isWrite, true);
assertEq(records[0].storageAccesses[0].previousValue, bytes32(uint(0)));
assertEq(records[0].storageAccesses[0].newValue, bytes32(uint(100)));
assertEq(records[0].storageAccesses[0].reverted, false);
```

请注意，此示例中没有 `Resume` 帐户访问。

### 示例：恢复的帐户访问
```solidity
contract Foo {
    Bar b;
    uint256 public val;
    constructor(Bar _b) { b = _b; }
    function run() external {
        val = val + 1;
        b.run();
        val = val + 1;
    }
}
contract Bar {
    function run() external {}
}

Bar bar = new Bar();
Foo foo = new Foo(bar);

vm.startStateDiffRecording();
foo.run();
Vm.AccountAccess[] memory records = vm.stopAndReturnStateDiff();

assertEq(records.length, 3);
Vm.AccountAccess memory fooCall = records[0];
assertEq(fooCall.kind, Vm.AccountAccessKind.Call);
assertEq(fooCall.account, address(foo));
assertEq(fooCall.accessor, address(this));
// foo.val increment
assertEq(fooCall.storageAccesses.length, 2);
assertEq(fooCall.storageAccesses[0].isWrite, false);
assertEq(fooCall.storageAccesses[1].isWrite, true);
assertEq(fooCall.storageAccesses[1].oldValue, bytes32(uint(0)));
assertEq(fooCall.storageAccesses[1].newValue, bytes32(uint(1)));

// bar.run CALL
Vm.AccountAccess memory barCall = records[1];
assertEq(barCall.kind, Vm.AccountAccessKind.Call);
assertEq(barCall.account, address(bar));
assertEq(barCall.accessor, address(foo));

// foo.run RESUME
Vm.AccountAccess memory fooResume = records[2];
assertEq(fooResume.kind, Vm.AccountAccessKind.Resume);
// foo.val increment
assertEq(fooResume.storageAccesses.length, 2);
assertEq(fooResume.storageAccesses[0].isWrite, false);
assertEq(fooResume.storageAccesses[1].isWrite, true);
assertEq(fooResume.storageAccesses[1].oldValue, bytes32(uint(1)));
assertEq(fooResume.storageAccesses[1].newValue, bytes32(uint(2)));
```