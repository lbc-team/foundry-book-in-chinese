## `envBool`

### 签名

```solidity
function envBool(string calldata key) external returns (bool value );
```

```solidity
function envBool(string calldata key, string calldata delimiter) external returns (bool[] memory values);
```

### 描述

将环境变量读取为 `bool` 或 `bool[]`。

### 提示

- 对于 `true`，可以使用 "true" 或 "True" 作为环境变量的值。
- 对于 `false`，可以使用 "false" 或 "False" 作为环境变量的值。
- 对于数组，你可以使用 `delimiter` 参数指定用于分隔值的分隔符。

### 例子

#### 单个值
使用环境变量 `BOOL_VALUE=true`，
```solidity
string memory key = "BOOL_VALUE";
bool expected = true;
bool output = cheats.envBool(key);
assert(output == expected);
```

#### 数组
使用环境变量 `BOOL_VALUES=true,false,True,False`，
```solidity
string memory key = "BOOL_VALUES";
string memory delimiter = ",";
bool[4] memory expected = [true, false, true, false];
bool[] memory output = cheats.envBool(key, delimiter);
assert(keccak256(abi.encodePacked((output))) == keccak256(abi.encodePacked((expected))));
```