## `expectCall`

```solidity
function expectCall(address where , bytes calldata data) external;
```

```solidity
function expectCall(address where, bytes calldata data, uint64 count) external;
```

```solidity
function expectCall(
    address where,
    uint256 value,
    bytes calldata data
) external;
```

```solidity
function expectCall(
    address where,
    uint256 value,
    bytes calldata data,
    uint64 count
) external;
```

### 描述 

期望调用指定地址 `where`，其中调用数据严格或宽松匹配 `data`。作弊码可以以两种方式调用：

- 如果未指定 `count` 参数，则期望调用至少与作弊码调用的次数相同。对于相同的调用数据，不能先使用没有 `count` 的作弊码调用，然后再传入 `count` 参数。
- 如果指定了 `count`，则期望调用严格地进行 `count` 次。对于相同的调用数据，不能通过调用没有 `count` 参数的作弊码来覆盖 `count` 值，也不能通过调用没有 `count` 参数的作弊码来增加 `count` 值。

`count` 也可以设置为 0，以断言未进行调用。

当对 `where` 进行调用时，首先检查调用数据是否与 `data` 完全匹配。如果不匹配，则检查调用数据是否部分匹配，匹配从调用数据的第一个字节开始。

**使用第二个签名**，我们还可以检查调用是否使用了预期的 `msg.value`。

如果测试在未进行调用的情况下终止，则测试失败。

> ℹ️ **内部调用**
>
> 此作弊码目前无法在内部调用上使用。请参阅问题 [#432](https://github.com/foundry-rs/foundry/issues/432)。

### 示例 

期望在代币 `MyToken` 上调用 `transfer` 一次：

```solidity
address alice = makeAddr("alice");
emit log_address(alice);
vm.expectCall(
  address(token), abi.encodeCall(token.transfer, (alice, 10))
);
token.transfer(alice, 10);
// [PASS]
```

期望在代币 `MyToken` 上调用 `transfer` *至少* 两次：

```solidity
address alice = makeAddr("alice");
emit log_address(alice);
vm.expectCall(
  address(token), abi.encodeCall(token.transfer, (alice, 10))
);
vm.expectCall(
  address(token), abi.encodeCall(token.transfer, (alice, 10))
);
token.transfer(alice, 10);
token.transfer(alice, 10);
token.transfer(alice, 10);
// [PASS]
```

期望在代币 `MyToken` 上未调用 `transfer`：

```solidity
address alice = makeAddr("alice");
emit log_address(alice);
vm.expectCall(
  address(token), abi.encodeCall(token.transfer, (alice, 10)), 0
);
token.transferFrom(alice, address(0), 10);
// [PASS]
```

期望在代币 `MyToken` 上使用任何调用数据调用 `transfer` 2 次：

```solidity
address alice = makeAddr("alice");
emit log_address(alice);
vm.expectCall(
  address(token), abi.encodeWithSelector(token.transfer.selector), 2
);
token.transfer(alice, 10);
token.transfer(alice, 10);
// [PASS]
```

期望在 `Contract` 上调用 `pay`，并具有特定的 `msg.value` 和 `调用数据`：

```solidity
Contract target = new Contract();
vm.expectCall(
            address(target),
            1,
            abi.encodeWithSelector(target.pay.selector, 2)
        );
target.pay{value: 1}(2);
// [PASS]
```

期望在 `Contract` 上调用 `pay`，并具有特定的 `msg.value` 和 `调用数据` 3 次：

```solidity
Contract target = new Contract();
vm.expectCall(
            address(target),
            1,
            abi.encodeWithSelector(target.pay.selector, 2),
            3
        );
target.pay{value: 1}(2);
target.pay{value: 1}(2);
target.pay{value: 1}(2);
// [PASS]
```