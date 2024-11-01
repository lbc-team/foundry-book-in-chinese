## `breakpoint`

### 签名

```solidity
function breakpoint(string) external;
function breakpoint(string, bool) external;
```

### 描述

在调试器视图中设置断点。

调用 `vm.breakpoint('<char>, true)` 等同于 `vm.breakpoint('<char>)`，但调用 `vm.breakpoint('<char>, false)` 将会删除 `'<char>` 处的断点。

如果字符被覆盖，只有在执行步骤中访问的最后一个字符会被考虑。

### 示例

```solidity
function testBreakpoint() public {
    vm.breakpoint("a");
}
```

在测试环境中打开调试器，并按下 `'a`，将会在调用断点作弊码的地方设置调试器步骤。

![breakpoint a](../images/breakpoint.png)

### 另请参阅

[debugger](../forge/debugger.md) 