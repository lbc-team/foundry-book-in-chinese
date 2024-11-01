## `envAddress`

### 签名

```solidity
function envAddress(string calldata key) external returns (address value);
```

```solidity
function envAddress(string calldata key, string calldata delimiter) external returns (address[] memory values);
```

### 描述

读取环境变量作为 `address` 或 `address[]`。

### 提示

- 对于数组，您可以使用 `delimiter` 参数指定用于分隔值的分隔符。

### 例子

#### 单个值
使用环境变量 `ADDRESS_VALUE=0x7109709ECfa91a80626fF3989D68f67F5b1DD12D`，
```solidity
string memory key = "ADDRESS_VALUE";
address expected = 0x7109709ECfa91a80626fF3989D68f67F5b1DD12D;
address output = vm.envAddress(key);
assert(output == expected);
```

#### 数组
使用环境变量 `ADDRESS_VALUES=0x7109709ECfa91a80626fF3989D68f67F5b1DD12D,0x0000000000000000000000000000000000000000`，
```solidity
string memory key = "ADDRESS_VALUES";
string memory delimiter = ",";
address[2] memory expected = [
    0x7109709ECfa91a80626fF3989D68f67F5b1DD12D,
    0x0000000000000000000000000000000000000000
];
address[] memory output = vm.envAddress(key, delimiter);
assert(keccak256(abi.encodePacked((output))) == keccak256(abi.encodePacked((expected))));
```