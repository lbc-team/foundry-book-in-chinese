## `toString`

### 签名

```solidity
function toString(address) external returns (string memory);
function toString(bool) external returns (string memory);
function toString(uint256) external returns (string memory);
function toString(int256) external returns (string memory);
function toString(bytes32) external returns (string memory);
function toString(bytes) external returns (string memory);
```

### 描述

将任何类型转换为其字符串版本。对于需要字符串的操作非常有用，比如作弊码 `ffi`。

将字节转换为其十六进制表示的字符串，以 `0x` 开头，表示它们是以十六进制编码的。

### 例子

```solidity
uint256 number = 420;
string memory stringNumber = vm.toString(number);
vm.assertEq(stringNumber, "420");
```

```solidity
bytes memory testBytes = hex"7109709ECfa91a80626fF3989D68f67F5b1DD12D";
string memory stringBytes = cheats.toString(testBytes);
assertEq("0x7109709ecfa91a80626ff3989d68f67f5b1dd12d", stringBytes);
```

```solidity
address testAddress =  0x7109709ECfa91a80626fF3989D68f67F5b1DD12D;
string memory stringAddress = cheats.toString(testAddress);
assertEq("0x7109709ECfa91a80626fF3989D68f67F5b1DD12D", stringAddress);
```
