## `mockFunction`

### 签名

```solidity
function mockFunction(address callee, address target, bytes calldata data) external;
```

### 描述

如果调用数据严格或宽松地匹配 `data`，则执行对地址 `callee` 的调用，使用地址 `target` 的字节码。

当对 `callee` 进行调用时，首先检查调用数据是否与 `data` 完全匹配。
如果不匹配，则检查调用数据是否在函数选择器上有部分匹配。

如果找到匹配，则使用 `target` 地址的字节码执行调用。

> ℹ️ **隔离测试**
>
> 如果使用隔离测试模式，此作弊码当前无法工作。

### 示例

对于两个合约（具有相同的存储布局）：
```solidity
contract Counter {
    uint256 public a;

    function count(uint256 x) public {
        a = 321 + x;
    }
}

contract ModelCounter {
    uint256 public a;

    function count(uint256 x) public {
        a = 123 + x;
    }
}
```
模拟对 `count` 函数的精确调用：

```solidity
function testMockFunction() public {
    vm.mockFunction(
        address(counter),
        address(model),
        abi.encodeWithSelector(Counter.count.selector, 456)
    );
    counter.count(456);
    assertEq(counter.a(), 123 + 456);
    counter.count(567);
    assertEq(counter.a(), 321 + 567);
}
```

模拟对 `count` 函数的所有调用：

```solidity
function testMockCall() public {
    vm.mockFunction(
        address(counter),
        address(model),
        abi.encodeWithSelector(Counter.count.selector)
    );
    counter.count(678);
    assertEq(counter.a(), 123 + 678);
    counter.count(789);
    assertEq(counter.a(), 123 + 789);
}
```