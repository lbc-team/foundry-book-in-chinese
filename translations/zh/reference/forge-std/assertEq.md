## `assertEq`

### 签名

#### `bool`

```solidity
function assertEq(bool left, bool right) internal;
```

```solidity
function assertEq(bool left, bool right, string memory err) internal;
```

#### `uint256`

```solidity
function assertEq(uint256 left, uint256 right) internal;
```

```solidity
function assertEq(uint256 left, uint256 right, string memory err) internal;
```

#### `int256`

```solidity
function assertEq(int256 left, int256 right) internal;
```

```solidity
function assertEq(int256 left, int256 right, string memory err) internal;
```

#### `address`

```solidity
function assertEq(address left, address right) internal;
```

```solidity
function assertEq(address left, address right, string memory err) internal;
```

#### `bytes32`

```solidity
function assertEq(bytes32 left, bytes32 right) internal;
```

```solidity
function assertEq(bytes32 left, bytes32 right, string memory err) internal;
```

#### `string`

```solidity
function assertEq(string memory left, string memory right) internal;
```

```solidity
function assertEq(string memory left, string memory right, string memory err) internal;
```

#### `bytes`

```solidity
function assertEq(bytes memory left, bytes memory right) internal;
```

```solidity
function assertEq(bytes memory left, bytes memory right, string memory err) internal;
```

#### `bool[]`

```solidity
function assertEq(bool[] memory left, bool[] memory right) internal;
```

```solidity
function assertEq(bool[] memory left, bool[] memory right, string memory err) internal;
```

#### `uint256[]`

```solidity
function assertEq(uint256[] memory left, uint256[] memory right) internal;
```

```solidity
function assertEq(uint256[] memory left, uint256[] memory right, string memory err) internal;
```

#### `int256[]`

```solidity
function assertEq(int256[] memory left, int256[] memory right) internal;
```

```solidity
function assertEq(int256[] memory left, int256[] memory right, string memory err) internal;
```

#### `address[]`

```solidity
function assertEq(address[] memory left, address[] memory right) internal;
```

```solidity
function assertEq(address[] memory left, address[] memory right, string memory err) internal;
```

#### `bytes32[]`

```solidity
function assertEq(bytes32[] memory left, bytes32[] memory right) internal;
```

```solidity
function assertEq(bytes32[] memory left, bytes32[] memory right, string memory err) internal;
```

#### `string[]`

```solidity
function assertEq(string[] memory left, string[] memory right) internal;
```

```solidity
function assertEq(string[] memory left, string[] memory right, string memory err) internal;
```

#### `bytes[]`

```solidity
function assertEq(bytes[] memory left, bytes[] memory right) internal;
```

```solidity
function assertEq(bytes[] memory left, bytes[] memory right, string memory err) internal;
```
#### 已过时

```solidity
// 已过时的用于断言两个小于 256 位的 uint：`assertEqUint(uint8(1), uint128(1));`
function assertEqUint(uint256 a, uint256 b) internal;
```

```solidity
function assertEq32(bytes32 left, bytes32 right) internal;
```

```solidity
function assertEq32(bytes32 left, bytes32 right, string memory err) internal;
```

### 描述

断言 `left` 等于 `right`。

可选地在回退字符串中包含错误消息。

### 参见

 - [`assertEqDecimal`](./assertEqDecimal.md)
 - [`assertNotEq`](./assertNotEq.md)