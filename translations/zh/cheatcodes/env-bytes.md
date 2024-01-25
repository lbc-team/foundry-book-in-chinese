## `envBytes`

### 签名

``` solidity
function envBytes(bytes calldata key) external returns (bytes value);
```

```solidity
function envBytes(bytes calldata key, bytes calldata delimiter) external returns (bytes[] memory values);
```

### 描述

读取环境变量作为 `bytes` 或 `bytes[]`。

### 提示

- 对于数组，您可以使用 `delimiter` 参数指定用于分隔值的分隔符。

### 例子

#### 单个值
使用环境变量 `BYTES_VALUE=0x7109709ECfa91a80626fF3989D68f67F5b1DD12D`；
```solidity
bytes memory key = "BYTES_VALUE";
bytes expected = hex"7109709ECfa91a80626fF3989D68f67F5b1DD12D";
bytes output = cheats.envBytes(key);
assertEq(output, expected);
```

#### 数组
使用环境变量 `BYTES_VALUE=0x7109709ECfa91a80626fF3989D68f67F5b1DD12D,0x00`；
```solidity
bytes memory key = "BYTES_VALUES";
bytes memory delimiter = ",";
bytes[] memory expected = new bytes[](2);
expected[0] = hex"7109709ECfa91a80626fF3989D68f67F5b1DD12D";
expected[1] = hex"00";
bytes[] memory output = cheats.envBytes(key, delimiter);
for (uint i = 0; i < expected.length; ++i) {
    assert(keccak256(abi.encodePacked((output[i]))) == keccak256(abi.encodePacked((expected[i]))));
}
```