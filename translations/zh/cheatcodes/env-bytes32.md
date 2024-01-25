## `envBytes32`

```solidity
function envBytes32(string calldata key) external returns (bytes32 value);
```

```solidity
function envBytes32(string calldata key, string calldata delimiter) external returns (bytes32[] memory values);
```

### 描述

以 `bytes32` 或 `address[]` 的形式读取环境变量。

### 提示

- 对于数组，您可以使用 `delimiter` 参数指定用于分隔值的分隔符。

### 例子

#### 单个值
使用环境变量 `BYTES32_VALUE=0x00`,
```solidity
string memory key = "BYTES32_VALUE";
bytes32 expected = bytes32(0x0000000000000000000000000000000000000000000000000000000000000000);
bytes32 output = cheats.envBytes32(key);
assert(output == expected);
```

#### 数组
使用环境变量 `BYTES32_VALUES=0x7109709ECfa91a80626fF3989D68f67F5b1DD12D,0x00`,
```solidity
string memory key = "BYTES32_VALUES";
string memory delimiter = ",";
bytes32[2] memory expected = [
    bytes32(0x7109709ECfa91a80626fF3989D68f67F5b1DD12D000000000000000000000000),
    bytes32(0x0000000000000000000000000000000000000000000000000000000000000000)
];
bytes32[] memory output = cheats.envBytes32(key, delimiter);
assert(keccak256(abi.encodePacked((output))) == keccak256(abi.encodePacked((expected))));
```