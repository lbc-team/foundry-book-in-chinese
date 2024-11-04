## `assertNotEq`

### 签名

#### `bool`

```solidity
function assertNotEq(bool left, bool right) internal;
```

```solidity
function assertNotEq(bool left, bool right, string memory err) internal;
```

#### `uint256`

```solidity
function assertNotEq(uint256 left, uint256 right) internal;
```

```solidity
function assertNotEq(uint256 left, uint256 right, string memory err) internal;
```

#### `int256`

```solidity
function assertNotEq(int256 left, int256 right) internal;
```

```solidity
function assertNotEq(int256 left, int256 right, string memory err) internal;
```

#### `address`

```solidity
function assertNotEq(address left, address right) internal;
```

```solidity
function assertNotEq(address left, address right, string memory err) internal;
```

#### `bytes32`

```solidity
function assertNotEq(bytes32 left, bytes32 right) internal;
```

```solidity
function assertNotEq(bytes32 left, bytes32 right, string memory err) internal;
```

#### `string`

```solidity
function assertNotEq(string memory left, string memory right) internal;
```

```solidity
function assertNotEq(string memory left, string memory right, string memory err) internal;
```

#### `bytes`

```solidity
function assertNotEq(bytes memory left, bytes memory right) internal;
```

```solidity
function assertNotEq(bytes memory left, bytes memory right, string memory err) internal;
```

#### `bool[]`

```solidity
function assertNotEq(bool[] memory left, bool[] memory right) internal;
```

```solidity
function assertNotEq(bool[] memory left, bool[] memory right, string memory err) internal;
```

#### `uint256[]`

```solidity
function assertNotEq(uint256[] memory left, uint256[] memory right) internal;
```

```solidity
function assertNotEq(uint256[] memory left, uint256[] memory right, string memory err) internal;
```

#### `int256[]`

```solidity
function assertNotEq(int256[] memory left, int256[] memory right) internal;
```

```solidity
function assertNotEq(int256[] memory left, int256[] memory right, string memory err) internal;
```

#### `address[]`

```solidity
function assertNotEq(address[] memory left, address[] memory right) internal;
```

```solidity
function assertNotEq(address[] memory left, address[] memory right, string memory err) internal;
```

#### `bytes32[]`

```solidity
function assertNotEq(bytes32[] memory left, bytes32[] memory right) internal;
```

```solidity
function assertNotEq(bytes32[] memory left, bytes32[] memory right, string memory err) internal;
```

#### `string[]`

```solidity
function assertNotEq(string[] memory left, string[] memory right) internal;
```

```solidity
function assertNotEq(string[] memory left, string[] memory right, string memory err) internal;
```

#### `bytes[]`

```solidity
function assertNotEq(bytes[] memory left, bytes[] memory right) internal;
```

```solidity
function assertNotEq(bytes[] memory left, bytes[] memory right, string memory err) internal;
```

#### 旧版

```solidity
function assertNotEq32(bytes32 left, bytes32 right) internal;
```

```solidity
function assertNotEq32(bytes32 left, bytes32 right, string memory err) internal;
```

### 描述

断言 `left` 不等于 `right`。

可选地在回退字符串中包含错误消息。

### 参见

- [`assertNotEqDecimal`](./assertNotEqDecimal.md)
- [`assertEq`](./assertEq.md)