## chisel

### 名称

`chisel` - 在 REPL 环境中测试 Solidity 输入并接收详细反馈。

### 简介

`chisel` [*options*]

#### 子命令 (bin)

1. `chisel list`
    * 显示存储在 `~/.foundry/cache/chisel` 中的所有缓存会话。
1. `chisel load <id>`
    * 如果存在 `id = <id>` 的缓存会话，则启动 REPL 并加载相应的会话。
1. `chisel view <id>`
    * 如果存在 `id = <id>` 的缓存会话，则显示该会话的 REPL 合约源代码。
1. `chisel clear-cache`
    * 删除 `~/.foundry/cache/chisel` 目录中的所有缓存文件。这些会话将无法恢复，因此请谨慎使用此命令。

#### 标志

有关所有可用的环境配置标志，请参见 `man chisel` 或 `chisel --help` 。

### 说明

Chisel 是一个 Solidity REPL（"读取-评估-打印 循环 "的缩写），它允许开发人员编写和测试 Solidity 代码片段。它提供了一个交互式环境，用于编写和执行 Solidity 代码，同时还提供了一组内置命令，用于处理和调试您的代码。这使得它成为一个有用的工具，可以快速测试和实验 Solidity 代码，而无需启动 foundry 沙盒测试套件。

### 使用方法

要打开 chisel，只需执行 `chisel` 二进制文件。

然后输入有效的 Solidity 代码。除命令外，chisel 提示符有两种输入方式：
1. 表达式
    * 表达式是返回值的语句，也可以单独求值。例如：
      `1 << 8` 是一个表达式，其值为 `256` 的 `uint256` 。表达式将首先进行计算，并且在计算完成后不会在会话状态中保留。
    * 例子：
        * `address(0).balance`
        * `abi.encode(256, bytes32(0), "Chisel!")`
        * `myViewFunc(128)`
        * ...
1. 语句
    * 语句是指代码片段，旨在保持会话的状态。语句包括变量定义、调用不改变状态的函数并返回值，以及合约、函数、事件、错误、映射或结构体的定义。如果您希望将表达式作为语句进行评估，可以在末尾添加分号（`;`）。
    * 例子：
        * `uint256 a = 0xa57b`
        * `myStateMutatingFunc(128)` || `myViewFunc(128);` <- Notice the `;`
        * ```solidity
          function hash64(
            bytes32 _a,
            bytes32 _b
          ) internal pure returns (bytes32 _hash) { 
              assembly {
                  // Store the 64 bytes we want to hash in scratch space
                  mstore(0x00, _a)
                  mstore(0x20, _b)

                  // Hash the memory in scratch space
                  // and assign the result to `_hash`
                  _hash := keccak256(0x00, 0x40)
              }
          }
          ```
        * `event ItHappened(bytes32 indexed hash)`
        * `struct Complex256 { uint256 re; uint256 im; }`
        * ...
#### 可用的命令

```text
⚒️ Chisel help
=============
General
        !help | !h - Display all commands
        !quit | !q - Quit Chisel
        !exec <command> [args] | !e <command> [args] - Execute a shell command and print the output

Session
        !clear | !c - Clear current session source
        !source | !so - Display the source code of the current session
        !save [id] | !s [id] - Save the current session to cache
        !load <id> | !l <id> - Load a previous session ID from cache
        !list | !ls - List all cached sessions
        !clearcache | !cc - Clear the chisel cache of all stored sessions
        !export | !ex - Export the current session source to a script file
        !fetch <addr> <name> | !fe <addr> <name> - Fetch the interface of a verified contract on Etherscan
        !edit - Open the current session in an editor

Environment
        !fork <url> | !f <url> - Fork an RPC for the current session. Supply 0 arguments to return to a local network
        !traces | !t - Enable / disable traces for the current session

Debug
        !memdump | !md - Dump the raw memory of the current state
        !stackdump | !sd - Dump the raw stack of the current state
        !rawstack <var> | !rs <var> - Display the raw value of a variable's stack allocation. For variables that are > 32 bytes in length, this will display their memory pointer.
```

**常用**

`!help` | `!h`

显示所有命令。

`!quit` | `!q`

退出 Chisel。

`!exec <command> [args]` | `!e <command> [args]`

执行 shell 命令并打印输出。

例子：

```sh
➜ !e ls
CHANGELOG.md
LICENSE
README.md
TESTS.md
artifacts
cache
contracts
crytic-export
deploy
deploy-config
deployments
dist
echidna.yaml
forge-artifacts
foundry.toml
hardhat.config.ts
layout-lock.json
node_modules
package.json
scripts
slither.config.json
slither.db.json
src
tasks
test-case-generator
tsconfig.build.json
tsconfig.build.tsbuildinfo
tsconfig.json
```

**会话**

`!clear` | `!c`

清除当前会话源。

在底层，每个 Chisel 会话都有一个基础合约，在您输入语句时会进行修改。这个命令会清除该合约并将您的会话重置为默认状态。

`!source` | `!so`

显示当前会话的合约源代码。

正如前面所提到的，每个 Chisel 会话都有一个基础的合约。这个命令将显示该合约的源代码。

`!save [id]` | `!s [id]`

将当前会话保存到缓存中。

Chisel 允许缓存会话，如果你要在 Chisel 中测试更复杂的逻辑，或者想在稍后返回会话，缓存会话是非常有用的。所有缓存的 Chisel 会话都存储在 `~/.foundry/cache/chisel` 中。

如果没有提供 `id` 参数，Chisel 会自动为保存的会话分配一个数字 ID。

`!load <id>` | `!l <id>`

从缓存中加载之前的会话 ID。

该命令将从缓存中加载先前缓存的会话。除了会话的源代码，所有环境设置也会被加载。`id` 参数必须与 `~/.foundry/cache/chisel` 目录中现有的缓存会话相对应。

`!list` | `!ls`

显示所有缓存的会话。

此命令将显示 `~/.foundry/cache/chisel` 目录中所有缓存的 chisel 会话。

`!clearcache` | `!cc`

清除所有已存储会话的 chisel 缓存。

删除 `~/.foundry/cache/chisel` 目录中的所有缓存文件。这些会话是无法恢复的，因此请谨慎使用此命令。

`!export` | `!ex`

将当前会话源代码导出为脚本文件。

如果 `chisel` 是在 foundry 项目的根目录下执行的，则可以将当前会话导出到项目的 `scripts` 目录下的 foundry 脚本中。

`!fetch <addr> <name>` | `!fe <addr> <name>`

获取 Etherscan 上已验证合约的接口。

此命令将尝试从 Etherscan API 解析已验证合约 @ `<addr>` 的接口。如果成功，接口将被插入会话源代码，名称为 `<name>`。

目前，只能获取以太坊主网上已验证合约的接口。未来，Chisel 将支持从多个 Etherscan 支持的链上获取接口。

`!edit`

在编辑器中打开当前会话的 `run()` 函数。

chisel 将使用 `$EDITOR` 环境变量中定义的编辑器。

**环境**

`!fork <url>` | `!f <url>`

分叉当前会话的 RPC。提供 0 个参数可返回本地网络。

尝试分叉所提供 RPC 的状态。如果没有提供 URL，则返回使用空白的本地 devnet 状态。

`!traces` | `!t`

启用/禁用当前会话的跟踪。

启用跟踪功能后，在插入每个语句后，将打印 foundry 风格的调用追踪和日志信息。

**调试**

`!memdump` | `!md`

转储在原始内存的当前状态。

在 REPL 合约的 run 函数执行完最后一条指令后，尝试转储机器原始内存的状态。

`!stackdump` | `!sd`

转储原始堆栈的当前状态。

在 REPL 合约的 `run` 函数的最后一条指令执行完毕后，尝试转储机器原始堆栈的状态。

`!rawstack <var>` | `!rs <var>`

显示变量堆栈分配的原始值。对于长度大于 32 字节的变量，将显示其内存指针。

如果要查看长度小于 32 字节的变量的全部原始堆栈分配，这条命令非常有用。

例子：

```sh
➜ address addr
➜ assembly {
    addr := not(0)
}
➜ addr
Type: address
└ Data: 0xffffffffffffffffffffffffffffffffffffffff
➜ !rs addr
Type: bytes32
└ Data: 0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
➜ 
```
