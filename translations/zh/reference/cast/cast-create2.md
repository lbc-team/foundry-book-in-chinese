## cast create2

### 名称

cast-create2 - 使用 CREATE2 生成确定性合约地址

### 概要

``cast create2`` [*选项*]

### 描述

使用 CREATE2 生成确定性合约地址

### 选项

`--starts-with` *hex*
&nbsp;&nbsp;&nbsp;&nbsp;合约地址的前缀。

`--ends-with` *hex*
&nbsp;&nbsp;&nbsp;&nbsp;合约地址的后缀。

`--matching` *hex*
&nbsp;&nbsp;&nbsp;&nbsp;地址必须匹配的序列

`--case-sensitive`
&nbsp;&nbsp;&nbsp;&nbsp;区分大小写的匹配

`--deployer` *address*
&nbsp;&nbsp;&nbsp;&nbsp;合约部署者的地址 [默认: `0x4e59b44847b379578588920ca78fbf26c0b4956c`]

`--init-code` *hex*
&nbsp;&nbsp;&nbsp;&nbsp;要部署的合约的初始化代码

`--init-code-hash` *hash*
&nbsp;&nbsp;&nbsp;&nbsp;要部署的合约的初始化代码哈希

`--jobs` *jobs*
&nbsp;&nbsp;&nbsp;&nbsp;要使用的线程数。默认为逻辑核心数并限制在该数目

`--caller` *address*
&nbsp;&nbsp;&nbsp;&nbsp;调用者的地址。用于盐的前 20 个字节

{{#include common-options.md}}

### 示例

1. 生成一个以 `dead` 开头的合约地址：
    ```sh
    cast create2 --starts-with dead
    ```
2. 生成一个以 `beef` 结尾的合约地址：
    ```sh
    cast create2 --ends-with beef
    ```
3. 更复杂的例子：
    ```sh
    cast create2 --starts-with dead --case-sensitive --deployer 0x0000000000FFe8B47B3e2130213B802212439497 --init-code-hash 0x0c591f26891d6443cf08c5be3584c1e6ae10a4c2f07c5c53218741e9755fb9cd
    ```

### 参见

[cast](./cast.md), [cast compute-address](./cast-compute-address.md)