## `expectEmit`

### 签名

```solidity
function expectEmit() external;
```

```solidity
function expectEmit(
    bool checkTopic1,
    bool checkTopic2,
    bool checkTopic3,
    bool checkData
) external;
```

```solidity
function expectEmit(address emitter) external;
```

```solidity
function expectEmit(
    bool checkTopic1,
    bool checkTopic2,
    bool checkTopic3,
    bool checkData,
    address emitter
) external;
```

### 描述

在下一次调用期间断言特定日志被发出。

1. 调用作弊码，指定我们是否应检查第一个、第二个或第三个主题，以及日志数据（`expectEmit()` 会检查它们全部）。主题 0 总是被检查。
2. 发出我们在下一次调用中应该看到的事件。
3. 执行调用。

你可以多次执行步骤 1 和 2，以匹配下一次调用中的事件序列。

如果事件在当前范围内不可用（例如，如果我们正在使用接口或外部智能合约），我们可以使用相同的事件签名自己定义事件。

`expectEmit` 有两种变体：

- **不检查发出者地址**：断言主题匹配，**不** 检查发出地址。
- **带 `address`**：断言主题匹配，并且发出地址匹配。

> ℹ️ **匹配序列**
>
> 在发出大量事件的函数中，有可能“跳过”事件并且只匹配特定序列，但是这个序列必须始终保持顺序。例如，假设一个函数发出事件：`A, B, C, D, E, F, F, G`。
>
> `expectEmit` 能够匹配带有或不带有在中间跳过事件的范围：
> - `[A, B, C]` 是有效的。
> - `[B, D, F]` 是有效的。
> - `[G]` 或任何其他单个事件组合都是有效的。
> - `[B, A]` 或类似的无序组合是 **无效** 的（事件必须按顺序）。
> - `[C, F, F]` 是有效的。
> - `[F, F, C]` 是 **无效** 的（无序）。

### 示例

这不检查发出地址。

```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);

function testERC20EmitsTransfer() public {
    vm.expectEmit();

    // We emit the event we expect to see.
    emit MyToken.Transfer(address(this), address(1), 10);

    // We perform the call.
    myToken.transfer(address(1), 10);
}
```

这会检查发出地址。

```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);

function testERC20EmitsTransfer() public {
    // We check that the token is the event emitter by passing the address.
    vm.expectEmit(address(myToken));
    emit MyToken.Transfer(address(this), address(1), 10);

    // We perform the call.
    myToken.transfer(address(1), 10);
}
```

我们还可以断言在单次调用中发出多个事件。

```solidity
function testERC20EmitsBatchTransfer() public {
    // We declare multiple expected transfer events
    for (uint256 i = 0; i < users.length; i++) {
        // Here we use the longer signature for demonstration purposes. This call checks
        // topic0 (always checked), topic1 (true), topic2 (true), NOT topic3 (false), and data (true).
        vm.expectEmit(true, true, false, true);
        emit Transfer(address(this), users[i], 10);
    }

    // We also expect a custom `BatchTransfer(uint256 numberOfTransfers)` event.
    vm.expectEmit(false, false, false, true);
    emit BatchTransfer(users.length);

    // We perform the call.
    myToken.batchTransfer(users, 10);
}
```

这个示例失败了，因为预期的事件在下一次调用中没有被发出。
```solidity
event Transfer(address indexed from, address indexed to, uint256 amount);

function testERC20EmitsTransfer() public {
    // We check that the token is the event emitter by passing the address as the fifth argument.
    vm.expectEmit(true, true, false, true, address(myToken));
    emit MyToken.Transfer(address(this), address(1), 10);

    // We perform an unrelated call that won't emit the intended event,
    // making the cheatcode fail.
    myToken.approve(address(this), 1e18);
    // We perform the call, but it will have no effect as the cheatcode has already failed.
    myToken.transfer(address(1), 10);
}
```