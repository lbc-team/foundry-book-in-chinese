## `skip`

## 签名

```solidity
function skip(bool skip) external;
```

## 描述

条件性地将测试标记为已跳过。必须在测试的顶部调用它，以确保在没有任何执行的情况下跳过测试。

如果使用 false 布尔值调用 `skip`，它将不会跳过测试。

标记为已跳过的测试将在测试运行器和摘要上显示 `[SKIPPED]` 标签，以便轻松识别已跳过的测试。

### 示例

```solidity

function testSkip() public {
    vm.skip(true);
    /// This revert will not be reached as this test will be skipped.
    revert("Should not reach this revert");
}

function testNotSkip() public {
    vm.skip(false);
    /// This revert will be reached as this test will not be skipped, and the test will fail.
    revert("Should reach this revert");
}
```
