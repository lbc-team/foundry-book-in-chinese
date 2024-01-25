## `setEnv`

### 签名

```solidity
function setEnv(string calldata key, string calldata value) external;
```

### 描述

设置一个环境变量 `key=value`。

> ℹ️ **注意**
>
> 进程设置的环境变量只能被自身及其子进程访问。因此，调用 `setEnv` 仅会修改当前运行的 `forge` 进程的环境变量，并不会影响 shell（`forge` 的父进程），即在 `forge` 进程退出后不会持久存在。

### 提示

- 环境变量的键不能为空。
- 环境变量的键不能包含等号 `=` 或 NUL 字符 `\0`。
- 环境变量的值不能包含 NUL 字符 `\0`。

### 例子

```solidity
string memory key = "hello";
string memory val = "world";
cheats.setEnv(key, val);
```