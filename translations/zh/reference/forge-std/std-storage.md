## Std Storage

Std Storage 是一个库，使操纵存储变得容易。

要使用 Std Storage，请在您的测试合约中导入以下内容：

```solidity
import {stdStorage, StdStorage} from "forge-std/Test.sol";              
```

在你的测试合约中添加以下行：

```solidity
using stdStorage for StdStorage;
```

然后，通过 `stdstore` 实例访问 Std Storage。

### 功能

查询功能：

- [`target`](./target.md): 设置目标合约的地址
- [`sig`](./sig.md): 将函数的 4 字节选择器设置为静态调用
- [`with_key`](./with_key.md): 向函数传递一个参数（可以多次使用）。
- [`depth`](./depth.md): 设置值在 `tuple` 中的位置（例如，在 `struct` 中）。

终端功能：

- [`find`](./find.md): 返回槽编号
- [`checked_write`](./checked_write.md): 设置要写到存储槽的数据
- [`read_<type>`](./read.md): 从存储槽中读取值为 `<type>`。

### 例子

`playerToCharacter` 跟踪玩家的角色信息。

```solidity
// MetaRPG.sol

struct Character {
    string name;
    uint256 level;
}

mapping (address => Character) public playerToCharacter;
```

比方说，我们想把我们的角色的等级设置为 120。

```solidity
// MetaRPG.t.sol

stdstore
    .target(address(metaRpg))
    .sig("playerToCharacter(address)")
    .with_key(address(this))
    .depth(1)
    .checked_write(120);
```

### 限制因数

- 不支持访问打包的槽位

### 已知问题

- 如果 `tuple` 包含短于 32 字节的类型，可能找不到槽。