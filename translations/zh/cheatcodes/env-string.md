## `envString`

### 签名

```solidity
function envString(string calldata key) external returns (string value);
```

```solidity
function envString(string calldata key, string calldata delimiter) external returns (string[] memory values);
```

### 描述

读取环境变量作为 `string` 或 `string[]`。如果环境变量未定义，Forge 将失败，并显示以下错误消息：

> [FAIL. Reason: Failed to get environment variable `FOO` as type `string`: environment variable not found]

### 提示

- 你可以将环境变量放在 `.env` 文件中。运行 `forge test` 时，Forge 将在加载时自动加载它们。
- 对于数组，你可以使用 `delimiter` 参数指定用于分隔值的分隔符。
- 选择一个在字符串值中不出现的分隔符，以便它们可以被正确分隔。

### 例子

#### 单个值
使用环境变量 `STRING_VALUE=hello, world!`，

```solidity
string memory key = "STRING_VALUE";
string memory expected = "hello, world!";
string memory output = vm.envString(key);
assertEq(output, expected);
```

#### 数组
使用环境变量 `STRING_VALUES=hello, world!|0x7109709ECfa91a80626fF3989D68f67F5b1DD12D`;

```solidity
string memory key = "STRING_VALUES";
string memory delimiter = "|";
string[2] memory expected = [
    "hello, world!",
    "0x7109709ECfa91a80626fF3989D68f67F5b1DD12D"
];
string[] memory output = vm.envString(key, delimiter);
for (uint i = 0; i < expected.length; ++i) {
    assert(keccak256(abi.encodePacked((output[i]))) == keccak256(abi.encodePacked((expected[i]))));
}
```