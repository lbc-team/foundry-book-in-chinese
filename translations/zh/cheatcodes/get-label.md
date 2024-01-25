## `getLabel`

### 签名

```solidity
function getLabel(address) external returns (string memory);
```

### 描述

如果之前已经对地址进行了标记，则检索该地址的标签。如果没有，则返回带有 `unlabeled:` 前缀的地址。
