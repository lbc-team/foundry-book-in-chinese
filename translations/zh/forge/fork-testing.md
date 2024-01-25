## 分叉（Fork）测试

Forge 支持使用两种不同的方法在分叉环境中进行测试：

- [**分叉模式（Forking Mode）**](#forking-mode) — 通过`forge test --fork-url` 标志使用一个单独分叉进行所有测试
- [**分叉作弊码（Forking Cheatcodes）**](#forking-cheatcodes) — 通过 [forking 作弊码](../cheatcodes/forking.md) 在 Solidity 测试代码中直接创建、选择和管理多个分叉

使用哪种方法？ 分叉模式提供针对特定分叉环境运行整个测试套件，而分叉作弊码提供更大的灵活性和表现力，可以在测试中使用多个分叉。 您的特定用例和测试策略将有助于知晓使用哪种方法。

### 分叉模式

要在分叉环境（例如分叉的以太坊主网）中运行所有测试，请通过 --fork-url 标志传递 RPC URL：

```bash
forge test --fork-url <your_rpc_url>
```

以下值会更改以反映分叉时链的值：

- [`block_number`](../reference/config/testing.md#block_number)
- [`chain_id`](../reference/config/testing.md#chain_id)
- [`gas_limit`](../reference/config/testing.md#gas_limit)
- [`gas_price`](../reference/config/testing.md#gas_price)
- [`block_base_fee_per_gas`](../reference/config/testing.md#block_base_fee_per_gas)
- [`block_coinbase`](../reference/config/testing.md#block_coinbase)
- [`block_timestamp`](../reference/config/testing.md#block_timestamp)
- [`block_difficulty`](../reference/config/testing.md#block_difficulty)

可以使用 `--fork-block-number` 指定要从中分叉的区块高度：

```bash
forge test --fork-url <your_rpc_url> --fork-block-number 1
```

当您需要与现有合约进行交互时，分叉特别有用。 您可以选择以这种方式进行集成测试，就好像您在实际网络上一样。

#### 缓存（Caching）

如果同时指定了 `--fork-url` 和 `--fork-block-number`，那么该块的数据将被缓存以供将来的测试运行。

数据缓存在 `~/.foundry/cache/rpc/<chain name>/<block number>` 中。 要清除缓存，只需删除目录或运行 [`forge clean`](../reference/forge/forge-clean.md)（删除所有构建工件和缓存目录）。

也可以通过传递 `--no-storage-caching` 或通过配置 [`no_storage_caching`](../reference/config/testing.md#no_storage_caching) 和 `foundry.toml` 完全忽略缓存 [`rpc_storage_caching`](../reference/config/testing.md#rpc_storage_caching)。

#### 已改进的跟踪 traces

Forge 支持使用 Etherscan 在分叉环境中识别合约。

要使用此功能，请通过 `--etherscan-api-key` 标志传递 `Etherscan API ` 密钥：

```bash
forge test --fork-url <your_rpc_url> --etherscan-api-key <your_etherscan_api_key>
```

或者，您可以设置 `ETHERSCAN_API_KEY` 环境变量。

### 分叉作弊码

分叉作弊码允许您在 Solidity 测试代码中以编程方式进入分叉模式，而不是通过 `forge` CLI 参数配置分叉模式。 这些作弊码允许您在逐个测试的基础上使用分叉模式，并在测试中使用多个分叉，每个分叉都通过其自己唯一的 `uint256` 标识符进行识别。

#### 用法

重要的是要记住，_所有_测试函数都是隔离的，这意味着每个测试函数都使用  `setUp` 之后的_拷贝_状态执行，并在其自己的独立 EVM 中执行。

因此，在 setUp 期间创建的分支可用于测试。 下面的代码示例使用 [`createFork`](../cheatcodes/create-fork.md) 创建两个分叉，但没有在初始就选择一个。 每个 fork 都有一个唯一标识符 (`uint256 forkId`)，该标识符在首次创建时分配。

通过将该 forkId 传递给 [`selectFork`](../cheatcodes/select-fork.md) 来启用特定的分叉。

[`createSelectFork`](../cheatcodes/create-select-fork.md) 是 `createFork` 加上 `selectFork` 的单行代码。

一次只能有一个活动分叉，当前活动分叉的标识符可以通过 [`activeFork`](../cheatcodes/active-fork.md) 检索。

类似于 [`roll`](../cheatcodes/roll.md)，您可以使用 [`rollFork`](../cheatcodes/roll-fork.md) 设置分叉的 `block.number`。

要了解选择分叉时会发生什么，了解分叉模式的一般工作方式很重要：

每个分叉都是一个独立的 EVM，即所有分叉都使用完全独立的存储。 唯一的例外是 `msg.sender` 的状态和测试合约本身，它们在分叉更换中是持久的。
换句话说，在 fork `A` 处于活动状态（`selectFork(A)`）时所做的所有更改仅记录在 fork `A` 的存储中，如果选择了另一个 fork，则不可用。 但是，测试合约本身（变量）中记录的更改仍然可用，因为测试合约是一个 _持久（persistent）_ 帐户。

`selectFork` 作弊码将 _remote_ 部分设置为分叉的数据源，但是 _本地（local）_ 内存在分叉更换期间保持不变。 这也意味着可以使用任何分叉随时调用 selectFork，以设置_remote_ 数据源。 但是，重要的是要记住上述 `读/写`访问规则始终适用，这意味着_写_在分叉更换中是持久的。

#### 例子

##### 创建和选择分叉


```solidity
contract ForkTest is Test {
    // the identifiers of the forks
    uint256 mainnetFork;
    uint256 optimismFork;
    
    //Access variables from .env file via vm.envString("varname")
    //Replace ALCHEMY_KEY by your alchemy key or Etherscan key, change RPC url if need
    //inside your .env file e.g:
    //MAINNET_RPC_URL = 'https://eth-mainnet.g.alchemy.com/v2/ALCHEMY_KEY'
    //string MAINNET_RPC_URL = vm.envString("MAINNET_RPC_URL");
    //string OPTIMISM_RPC_URL = vm.envString("OPTIMISM_RPC_URL");

    // create two _different_ forks during setup
    function setUp() public {
        mainnetFork = vm.createFork(MAINNET_RPC_URL);
        optimismFork = vm.createFork(OPTIMISM_RPC_URL);
    }

    // demonstrate fork ids are unique
    function testForkIdDiffer() public {
        assert(mainnetFork != optimismFork);
    }

    // select a specific fork
    function testCanSelectFork() public {
        // select the fork
        vm.selectFork(mainnetFork);
        assertEq(vm.activeFork(), mainnetFork);

        // from here on data is fetched from the `mainnetFork` if the EVM requests it and written to the storage of `mainnetFork`
    }

    // manage multiple forks in the same test
    function testCanSwitchForks() public {
        vm.selectFork(mainnetFork);
        assertEq(vm.activeFork(), mainnetFork);

        vm.selectFork(optimismFork);
        assertEq(vm.activeFork(), optimismFork);
    }

    // forks can be created at all times
    function testCanCreateAndSelectForkInOneStep() public {
        // creates a new fork and also selects it
        uint256 anotherFork = vm.createSelectFork(MAINNET_RPC_URL);
        assertEq(vm.activeFork(), anotherFork);
    }

    // set `block.number` of a fork
    function testCanSetForkBlockNumber() public {
        vm.selectFork(mainnetFork);
        vm.rollFork(1_337_000);

        assertEq(block.number, 1_337_000);
    }
}
```

#### 分离的和持久存储(storage)

如前所述，每个分叉本质上都是一个独立的 EVM，具有独立的存储(storage)空间。

选择分叉时，只有 `msg.sender` 和测试合约（`ForkTest`）的账户是持久的。 但是任何帐户都可以变成持久帐户：[`makePersistent`](../cheatcodes/make-persistent.md)。

_persistent_ 帐户是唯一的， 即它存在于所有分叉上

```solidity
contract ForkTest is Test {
    // the identifiers of the forks
    uint256 mainnetFork;
    uint256 optimismFork;
    
    //Access variables from .env file via vm.envString("varname")
    //Replace ALCHEMY_KEY by your alchemy key or Etherscan key, change RPC url if need
    //inside your .env file e.g:
    //MAINNET_RPC_URL = 'https://eth-mainnet.g.alchemy.com/v2/ALCHEMY_KEY'
    //string MAINNET_RPC_URL = vm.envString("MAINNET_RPC_URL");
    //string OPTIMISM_RPC_URL = vm.envString("OPTIMISM_RPC_URL");

    // create two _different_ forks during setup
    function setUp() public {
        mainnetFork = vm.createFork(MAINNET_RPC_URL);
        optimismFork = vm.createFork(OPTIMISM_RPC_URL);
    }

    // creates a new contract while a fork is active
    function testCreateContract() public {
        vm.selectFork(mainnetFork);
        assertEq(vm.activeFork(), mainnetFork);
        
        // the new contract is written to `mainnetFork`'s storage
        SimpleStorageContract simple = new SimpleStorageContract();
        
        // and can be used as normal
        simple.set(100);
        assertEq(simple.value(), 100);
        
        // after switching to another contract we still know `address(simple)` but the contract only lives in `mainnetFork` 
        vm.selectFork(optimismFork);
        
        /* this call will therefore revert because `simple` now points to a contract that does not exist on the active fork
        * it will produce following revert message:
        * 
        * "Contract 0xCe71065D4017F316EC606Fe4422e11eB2c47c246 does not exist on active fork with id `1`
        *       But exists on non active forks: `[0]`"
        */
        simple.value();
    }
    
     // creates a new _persistent_ contract while a fork is active
     function testCreatePersistentContract() public {
        vm.selectFork(mainnetFork);
        SimpleStorageContract simple = new SimpleStorageContract();
        simple.set(100);
        assertEq(simple.value(), 100);
        
        // mark the contract as persistent so it is also available when other forks are active
        vm.makePersistent(address(simple));
        assert(vm.isPersistent(address(simple))); 
        
        vm.selectFork(optimismFork);
        assert(vm.isPersistent(address(simple))); 
        
        // This will succeed because the contract is now also available on the `optimismFork`
        assertEq(simple.value(), 100);
     }
}

contract SimpleStorageContract {
    uint256 public value;

    function set(uint256 _value) public {
        value = _value;
    }
}
```

有关更多详细信息和示例，请参阅 [forking 作弊码](../cheatcodes/forking.md) 参考。
