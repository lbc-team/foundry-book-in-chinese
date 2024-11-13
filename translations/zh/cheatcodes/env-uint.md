## `envUint`

### 签名

```solidity
function envUint(string calldata key) external returns (uint256 value);
```

```solidity
function envUint(string calldata key, string calldata delimiter) external returns (uint256[] memory values);
```

### 描述

读取环境变量作为 `uint256` 或 `uint256[]`。

### 提示

- 如果值以 `0x` 开头，则将其解释为十六进制值，否则将视为十进制数。
- 对于数组，你可以使用 `delimiter` 参数指定用于分隔值的分隔符。

### 例子

#### 单个值
对于环境变量 `UINT_VALUE=115792089237316195423570985008687907853269984665640564039457584007913129639935`，
```solidity
string memory key = "UINT_VALUE";
uint256 expected = type(uint256).max;
uint256 output = cheats.envUint(key);
assert(output == expected);
```

#### 数组
对于环境变量 `UINT_VALUES=0,0x0000000000000000000000000000000000000000000000000000000000000000`，
```solidity
string memory key = "UINT_VALUES";
string memory delimiter = ",";
uint256[2] memory expected = [type(uint256).min, type(uint256).min];
uint256[] memory output = cheats.envUint(key, delimiter);
assert(keccak256(abi.encodePacked((output))) == keccak256(abi.encodePacked((expected))));
```