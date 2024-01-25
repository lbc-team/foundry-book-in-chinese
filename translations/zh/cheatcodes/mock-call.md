## `mockCall`

### 签名

```solidity
function mockCall(address where, bytes calldata data, bytes calldata retdata) external;
```

```solidity
function mockCall(
    address where,
    uint256 value,
    bytes calldata data,
    bytes calldata retdata
) external;
```

### 描述

如果调用数据严格或宽松匹配 `data`，则模拟对地址 `where` 的所有调用，并返回 `retdata`。

当对 `where` 进行调用时，首先检查调用数据是否与 `data` 完全匹配。
如果不匹配，则检查调用数据是否部分匹配，匹配从调用数据的第一个字节开始。

如果找到匹配项，则从调用返回 `retdata`。

**使用第二个签名**，我们可以模拟具有特定 `msg.value` 的调用。在存在歧义的情况下，`calldata` 匹配优先于 `msg.value`。

模拟的调用有效，直到调用 [`clearMockedCalls`](./clear-mocked-calls.md)。

> ℹ️ **注意**
>
> 对模拟地址的调用可能会引发还原，如果地址上没有代码。
> 这是因为 Solidity 在某些合约调用之前插入了 `extcodesize` 检查。
>
> 为了规避这一点，如果模拟地址没有代码，请使用[`etch`](./etch.md) cheatcode。

> ℹ️ **内部调用**
>
> 此作弊码目前无法在内部调用上使用。请参见问题 [#432](https://github.com/foundry-rs/foundry/issues/432)。

### 例子

模拟精确调用：

```solidity
function testMockCall() public {
    vm.mockCall(
        address(0),
        abi.encodeWithSelector(MyToken.balanceOf.selector, address(1)),
        abi.encode(10)
    );
    assertEq(IERC20(address(0)).balanceOf(address(1)), 10);
}
```

模拟整个函数：

```solidity
function testMockCall() public {
    vm.mockCall(
        address(0),
        abi.encodeWithSelector(MyToken.balanceOf.selector),
        abi.encode(10)
    );
    assertEq(IERC20(address(0)).balanceOf(address(1)), 10);
    assertEq(IERC20(address(0)).balanceOf(address(2)), 10);
}
```

模拟具有给定 `msg.value` 的调用：

```solidity
function testMockCall() public {
    assertEq(example.pay{value: 10}(1), 1);
    assertEq(example.pay{value: 1}(2), 2);
    vm.mockCall(
        address(example),
        10,
        abi.encodeWithSelector(example.pay.selector),
        abi.encode(99)
    );
    assertEq(example.pay{value: 10}(1), 99);
    assertEq(example.pay{value: 1}(2), 2);
}
```

模拟公开的参数：


```solidity
contract Example {
    uint256 public number = 10;
}

contract ExampleTest is Test {
    Example public example;

    function setUp() public {
        example = new Example();
    }

    function testMockPublicVariable() public {
        assertEq(example.number(), 10);
        vm.mockCall(
            address(example),
            abi.encodeWithSelector(bytes4(keccak256("number()"))),
            abi.encode(5)
        );
        assertEq(example.number(), 5);
    }
}
```