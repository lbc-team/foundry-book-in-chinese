## cast new-mnemonic

### 名称

cast-wallet-new-mnemonic - 创建具有一组指定数量单词的新助记词。

### 概要

``cast wallet new-mnemonic`` [*options*]

### 描述

生成一个随机的 BIP39 助记词短语。

### 选项

#### 新助记词选项

`-w`
`--words`
&nbsp;&nbsp;&nbsp;&nbsp;助记词的单词数量。默认为 12。

`-a`
`--accounts`
&nbsp;&nbsp;&nbsp;&nbsp;要显示的账户数量，这些账户是从助记词派生而来的。默认为 1。

### 示例

1. 使用 24 个单词创建一个新的助记词。
    ```sh
    cast wallet new-mnemonic --words 24
    ```
   
```text
Successfully generated a new mnemonic.
Phrase:
decrease where seek crop segment want icon medal sleep social blast provide virus grief pledge soccer stereo trick dry dirt rotate explain into nominee

Accounts:
- Account 0:
Address:     0x34644D4eC92ae1832877cE22AD9bA4b00c7397c8
Private key: 0x832a3784d0a130c8a0ce3cc6dfc336a41ca7801a117eac7a3bfaace52e4d239c
```

### 参见

[cast](./cast.md) 