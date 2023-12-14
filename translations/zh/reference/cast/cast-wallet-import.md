## cast wallet import

### 名称

cast-wallet-import - 将私钥导入加密的密钥库

### 概要

`cast wallet import` [*选项*] _账户名称_

### 描述

将私钥导入加密的密钥库。

如果未指定 _keystore-dir_，它将被保存在默认位置 `~/.foundry/keystores`，因此可以通过 `--account` 选项在诸如 `forge script`、`cast send` 或任何需要私钥的其他方法中访问。

### 选项

#### 目录选项

`-k`  
`--keystore-dir`

&nbsp;&nbsp;&nbsp;&nbsp;存储加密密钥库的路径。  
&nbsp;&nbsp;&nbsp;&nbsp;默认为 `~/.foundry/keystores`。

{{#include ../common/wallet-options-raw.md}}

{{#include common-options.md}}

### 示例

1. 从私钥创建密钥库：

   ```sh
   cast wallet import BOB --interactive
   ```

2. 从助记词创建密钥库：

   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test"
   ```

3. 从具有特定助记词索引的助记词创建密钥库：
   ```sh
   cast wallet import ALICE --mnemonic "test test test test test test test test test test test test" --mnemonic-index 1
   ```

### 参见

[cast](./cast.md), [cast wallet](./cast-wallet.md)