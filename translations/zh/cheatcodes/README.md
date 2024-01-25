## 作弊码参考

作弊码(Cheatcodes)为您提供强大的断言，改变 EVM 状态的能力，模拟数据等功能。

通过使用作弊码地址（`0x7109709ECfa91a80626fF3989D68f67F5b1DD12D`）来提供作弊码。

> ℹ️ **注意**
>
> 如果在测试中使用模糊地址时遇到此地址的错误，您可能希望通过使用以下行将其从模糊测试中排除：

>
> ```solidity
> vm.assume(address_ != 0x7109709ECfa91a80626fF3989D68f67F5b1DD12D);
> ```


您还可以通过 Forge 标准库的 [`Test`](../reference/forge-std/#forge-stds-test) 合约中提供的 `vm` 轻松访问作弊码。

### Forge 标准库作弊码

Forge 标准库实现了作弊码的包装器，这些包装器结合了多个标准作弊码，以改进开发体验。这些技术上不是作弊码，而是 Forge 作弊码的组合。

您可以在[参考部分](../reference/forge-std/std-cheats.md)中查看 Forge 标准库作弊码包装器的列表。您可以参考[Forge 标准库源代码](https://github.com/foundry-rs/forge-std/blob/master/src/Test.sol) 以了解包装器在内部是如何工作的。

### 作弊码类型

以下是有关不同 Forge 作弊码的一些子部分。

- [Environment](./environment.md): 改变以太坊虚拟机状态的作弊码。
- [Assertions](./assertions.md): 强大的断言作弊码
- [Fuzzer](./fuzzer.md): 配置模糊器的作弊码
- [External](./external.md): 与外部状态（文件、命令等）交互的作弊码
- [Utilities](./utilities.md): 较小的实用程序作弊码
- [Forking](./forking.md): 分叉模式作弊码
- [Snapshots](./snapshots.md): 快照作弊码
- [RPC](./rpc.md): 与 RPC 相关的作弊码
- [File](./fs.md): 用于处理文件的作弊码

### 添加新的作弊码

如果您需要新功能，请考虑[贡献到 Foundry 的代码库](../contributing.md)以添加作弊码。

### 作弊码接口

这是 Forge 中所有作弊码的 Solidity 接口。

```solidity
interface CheatCodes {
    // 允许使用getRecordedLogs()
    struct Log {
        bytes32[] topics;
        bytes data;
    }

    // 用于readCallers()的caller mode
    enum CallerMode {
        None,
        Broadcast,
        RecurrentBroadcast,
        Prank,
        RecurrentPrank
    }

    enum AccountAccessKind {
        Call,
        DelegateCall,
        CallCode,
        StaticCall,
        Create,
        SelfDestruct,
        Resume
    }

    struct Wallet {
        address addr;
        uint256 publicKeyX;
        uint256 publicKeyY;
        uint256 privateKey;
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

    // 从输入派生私钥，返回该输入命名的钱包
    function createWallet(string calldata) external returns (Wallet memory);

    // 从私钥派生钱包，返回钱包
    function createWallet(uint256) external returns (Wallet memory);

    // 从私钥派生钱包，返回该输入命名的钱包
    function createWallet(uint256, string calldata) external returns (Wallet memory);

    // 签发交易, (Wallet, digest) => (v, r, s)
    function sign(Wallet calldata, bytes32) external returns (uint8, bytes32, bytes32);

    // 获取钱包的 nonce 值
    function getNonce(Wallet calldata) external returns (uint64);
    
    // 设置 block.timestamp
    function warp(uint256) external;

    // 设置 block.number
    function roll(uint256) external;

    // 设置 block.basefee
    function fee(uint256) external;

    // 设置 block.difficulty
    function difficulty(uint256) external;
    
    // Set block.prevrandao
    // Does not work before the Paris hard fork, and will revert instead.
    function prevrandao(bytes32) external;

    // 设置 block.chainid
    function chainId(uint256) external;

    // 从对应地址加载 storage slot
    function load(address account, bytes32 slot) external returns (bytes32);

    // 向特定地址account的 storage slot 存储 value
    function store(address account, bytes32 slot, bytes32 value) external;

    // 签名数据
    function sign(uint256 privateKey, bytes32 digest)
        external
        returns (uint8 v, bytes32 r, bytes32 s);

    // 计算给定私钥对应的地址
    function addr(uint256 privateKey) external returns (address);

    // 从提供的助记词字符串或在该地址 m/44'/60'/0'/0/{index} 的助记词文件中生成私钥。
    function deriveKey(string calldata, uint32) external returns (uint256);
    // 从提供的助记词字符串或在该地址 m/44'/60'/0'/0/{index} 的助记词文件中生成私钥。
    function deriveKey(string calldata, string calldata, uint32) external returns (uint256);

    // 获取地址为account的nonce
    function getNonce(address account) external returns (uint64);

    // 设置地址为account的nonce
    // 新设置的nonce要求大于目前的nonce
    function setNonce(address account, uint64 nonce) external;

    // 通过终端执行外部调用
    function ffi(string[] calldata) external returns (bytes memory);

    // 设置环境变量, (name, value)
    function setEnv(string calldata, string calldata) external;

    // 设置环境变量, (name) => (value)
    function envBool(string calldata) external returns (bool);
    function envUint(string calldata) external returns (uint256);
    function envInt(string calldata) external returns (int256);
    function envAddress(string calldata) external returns (address);
    function envBytes32(string calldata) external returns (bytes32);
    function envString(string calldata) external returns (string memory);
    function envBytes(string calldata) external returns (bytes memory);

    // 以数组形式读取环境变量, (name, delim) => (value[])
    function envBool(string calldata, string calldata)
        external
        returns (bool[] memory);
    function envUint(string calldata, string calldata)
        external
        returns (uint256[] memory);
    function envInt(string calldata, string calldata)
        external
        returns (int256[] memory);
    function envAddress(string calldata, string calldata)
        external
        returns (address[] memory);
    function envBytes32(string calldata, string calldata)
        external
        returns (bytes32[] memory);
    function envString(string calldata, string calldata)
        external
        returns (string[] memory);
    function envBytes(string calldata, string calldata)
        external
        returns (bytes[] memory);

    // 以默认值读取环境变量, (name, value) => (value)
    function envOr(string calldata, bool) external returns (bool);
    function envOr(string calldata, uint256) external returns (uint256);
    function envOr(string calldata, int256) external returns (int256);
    function envOr(string calldata, address) external returns (address);
    function envOr(string calldata, bytes32) external returns (bytes32);
    function envOr(string calldata, string calldata) external returns (string memory);
    function envOr(string calldata, bytes calldata) external returns (bytes memory);
    
    // 以默认值和数组形势读取环境变量, (name, value[]) => (value[])
    function envOr(string calldata, string calldata, bool[] calldata) external returns (bool[] memory);
    function envOr(string calldata, string calldata, uint256[] calldata) external returns (uint256[] memory);
    function envOr(string calldata, string calldata, int256[] calldata) external returns (int256[] memory);
    function envOr(string calldata, string calldata, address[] calldata) external returns (address[] memory);
    function envOr(string calldata, string calldata, bytes32[] calldata) external returns (bytes32[] memory);
    function envOr(string calldata, string calldata, string[] calldata) external returns (string[] memory);
    function envOr(string calldata, string calldata, bytes[] calldata) external returns (bytes[] memory);

    // 将 Solidity 类型转换为字符串
    function toString(address) external returns(string memory);
    function toString(bytes calldata) external returns(string memory);
    function toString(bytes32) external returns(string memory);
    function toString(bool) external returns(string memory);
    function toString(uint256) external returns(string memory);
    function toString(int256) external returns(string memory);

    // 将 *下一次调用* 的 msg.sender 设置为输入地址
    function prank(address) external;

    // 在 `stopPrank` 执行前，设置所有后续调用的 msg.sender 为输入地址
    function startPrank(address) external;

    // 将 *下一次调用* 的 msg.sender 和 tx.origin 设置为输入地址
    function prank(address, address) external;

    // 在 `stopPrank` 执行前，设置所有后续调用的 msg.sender 和 tx.origin
    function startPrank(address, address) external;

    // 将后续调用的 msg.sender 重设为 `address(this)`
    function stopPrank() external;

    // 从当前状态读取的 `msg.sender` 和 `tx.origin`
    function readCallers() external returns (CallerMode callerMode, address msgSender, address txOrigin);
    
    // 设置地址余额
    function deal(address who, uint256 newBalance) external;

    // 设置地址代码(code)
    function etch(address who, bytes calldata code) external;
    
    // 将测试标记跳过
    function skip(bool skip) external;
    
    // 期望调用中出现的错误
    function expectRevert() external;
    function expectRevert(bytes calldata) external;
    function expectRevert(bytes4) external;

    // 记录所有 storage 的读写
    function record() external;

    // 获取给定地址的所有slot读写情况
    function accesses(address)
        external
        returns (bytes32[] memory reads, bytes32[] memory writes);
    
    // Record all account accesses as part of CREATE, CALL or SELFDESTRUCT opcodes in order,
    // along with the context of the calls.
    function startStateDiffRecording() external;

    // Returns an ordered array of all account accesses from a `startStateDiffRecording` session.
    function stopAndReturnStateDiff() external returns (AccountAccess[] memory accesses);

    // 记录所有交易 log
    function recordLogs() external;

    // 获取所有交易 log
    function getRecordedLogs() external returns (Log[] memory);

    // 预期如下形式的log触发:
    //   (bool checkTopic1, bool checkTopic2, bool checkTopic3, bool checkData)
    // 调用函数并触发事件的过程中，可以使用该作弊码检查log的触发顺序是否符合期望。
    // 第二种形式的调用是针对于特定地址进行检查
    function expectEmit(bool, bool, bool, bool) external;
    function expectEmit(bool, bool, bool, bool, address) external;

    // 模拟对特定地址的调用并返回特定数据
    // 如果不明确期望调用的函数签名，那么所有函数都将会被模拟调用。
    function mockCall(address, bytes calldata, bytes calldata) external;

    // 回滚模拟调用
    function clearMockedCalls() external;

    // 期望某个针对address的特定调用会返回特定数据
    function expectCall(address, bytes calldata) external;
    
    function expectCall(address, uint256, bytes calldata) external;

    // 给定 json 文件的相对路径，获取合约创建字节码
    function getCode(string calldata) external returns (bytes memory);
    // 给定 json 文件的相对路径，获取合约部署后的字节码
    function getDeployedCode(string calldata) external returns (bytes memory);

    // 在测试 traces 中标记特定地址
    function label(address addr, string calldata label) external;
    
    // Retrieve the label of an address
    function getLabel(address addr) external returns (string memory);

    // 在模糊测试中当条件不满足时生成新测试输入
    function assume(bool) external;

    // 设置 block.coinbase (who)
    function coinbase(address) external;

    // 使用调用地址或者入参地址作为下一次调用的调用者，创建交易并将其签名并发送。
    function broadcast() external;
    function broadcast(address) external;

    // 使用调用地址或者入参地址作为后续调用的调用者，创建交易并将其签名并发送。
    function startBroadcast() external;
    function startBroadcast(address) external;
    function startBroadcast(uint256 privateKey) external;

    // 停止上述交易发送
    function stopBroadcast() external;

    // 读取文件内容为字符串, (path) => (data)
    function readFile(string calldata) external returns (string memory);
    // 获取当前项目的根目录
    function projectRoot() external returns (string memory);
    // 读取文件下一行作为字符串, (path) => (line)
    function readLine(string calldata) external returns (string memory);
    // 向文件写入数据，如文件不存在则创建，如文件已有数据将会被覆盖。(path, data) => ()
    function writeFile(string calldata, string calldata) external;
    // 向文件写入一行数据，如文件不存在则创建。 (path, data) => ()
    function writeLine(string calldata, string calldata) external;
    // 关闭读取的文件并将指针偏移恢复至文件开头 (path) => ()
    function closeFile(string calldata) external;
    // 删除文件，但如下情况将不会执行：
    // - 路径指针指向文件夹
    // - 文件不存在
    // - 用户缺少删除文件权限.
    // (path) => ()
    function removeFile(string calldata) external;
    // Returns true if the given path points to an existing entity, else returns false
    // (path) => (bool)
    function exists(string calldata) external returns (bool);
    // Returns true if the path exists on disk and is pointing at a regular file, else returns false
    // (path) => (bool)
    function isFile(string calldata) external returns (bool);
    // Returns true if the path exists on disk and is pointing at a directory, else returns false
    // (path) => (bool)
    function isDir(string calldata) external returns (bool);
    
    // 返回对应 'key' 的值
    function parseJson(string memory json, string memory key) external returns (bytes memory);
    // 返回整个 json 文件
    function parseJson(string memory json) external returns (bytes memory);
    // Check if a key exists in a json string
    function keyExists(string memory json, string memory key) external returns (bytes memory);
    // Get list of keys in a json string
    function parseJsonKeys(string memory json, string memory key) external returns (string[] memory);

    // 快照保存当前EVM状态，返回创建的快照id
    // 恢复快照状态使用 `revertTo`
    function snapshot() external returns (uint256);
    // 恢复之前存储的快照状态，输入为快照对应的id。
    // 这将会删除给定快照之后存储的所有快照。
    function revertTo(uint256) external returns (bool);

    // 在给定区块创建新分叉并返回对应的标识符。
    function createFork(string calldata, uint256) external returns (uint256);
    // 在最新区块创建新分叉并返回对应的标识符。
    function createFork(string calldata) external returns (uint256);

    // 在给定区块创建并进入新分叉，随后返回对应的标识符。
    function createSelectFork(string calldata, uint256)
        external
        returns (uint256);
    // 在最新区块创建并进入新分叉，随后返回对应的标识符。
    function createSelectFork(string calldata) external returns (uint256);

    // 选择并将对应的分叉设置为 active
    function selectFork(uint256) external;

    // 返回当前设置为 active 的分叉
    function activeFork() external returns (uint256);

    // 更新当前分叉至给定区块号
    function rollFork(uint256) external;

    function rollFork(uint256 forkId, uint256 blockNumber) external;

    // 从 active 分叉中获取给定的交易，并在当前状态上执行
    function transact(bytes32) external;
    // 从给定分叉中获取给定的交易，并在当前状态上执行
    function transact(uint256, bytes32) external;

    // 标记账户使其在跨分叉时保留存储状态
    function makePersistent(address) external;
    function makePersistent(address, address) external;
    function makePersistent(address, address, address) external;
    function makePersistent(address[] calldata) external;
    // 取消通过 `makePersistent` 设置跨分支持久状态
    function revokePersistent(address) external;
    function revokePersistent(address[] calldata) external;
    // 返回 true 如果账户被标记为 persistent
    function isPersistent(address) external returns (bool);

    /// 返回 RPC url
    function rpcUrl(string calldata) external returns (string memory);
    /// 返回所有的 rpc urls `[alias, url][]`
    function rpcUrls() external returns (string[2][] memory);
}
```
