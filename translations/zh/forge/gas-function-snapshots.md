## gas 函数快照

Forge 可以为你所有的测试函数生成 gas 快照。这对于了解你的合约将消耗多少 gas，或在各种优化前后比较 gas 使用情况非常有用。

要生成 gas 快照，请运行 [`forge snapshot`](../reference/forge/forge-snapshot.md)。

这将默认生成一个名为 `.gas-snapshot` 的文件，其中包含你所有的测试及其各自的 gas 使用情况。

```ignore
$ forge snapshot
$ cat .gas-snapshot

ERC20Test:testApprove() (gas: 31162)
ERC20Test:testBurn() (gas: 59875)
ERC20Test:testFailTransferFromInsufficientAllowance() (gas: 81034)
ERC20Test:testFailTransferFromInsufficientBalance() (gas: 81662)
ERC20Test:testFailTransferInsufficientBalance() (gas: 52882)
ERC20Test:testInfiniteApproveTransferFrom() (gas: 90167)
ERC20Test:testMetadata() (gas: 14606)
ERC20Test:testMint() (gas: 53830)
ERC20Test:testTransfer() (gas: 60473)
ERC20Test:testTransferFrom() (gas: 84152)
```

### 过滤

如果你想指定一个不同的输出文件，请运行 `forge snapshot --snap <FILE_NAME>`。

你还可以按 gas 使用情况对结果进行排序。使用 `--asc` 选项按升序排序结果，使用 `--desc` 选项按降序排序结果。

最后，你还可以为所有测试指定最小/最大 gas 阈值。要仅包含高于阈值的结果，可以使用 `--min <VALUE>` 选项。同样，要仅包含低于阈值的结果，可以使用 `--max <VALUE>` 选项。

请记住，所做的更改将保存在快照文件中，而不是在你屏幕上显示的快照中。

你还可以将其与 `forge test` 的过滤器结合使用，例如 `forge snapshot --match-path contracts/test/ERC721.t.sol` 以生成与此测试合约相关的 gas 快照。

### 比较 gas 使用情况

如果你想将当前快照文件与你的最新更改进行比较，可以使用 `--diff` 或 `--check` 选项。

`--diff` 将与快照进行比较并显示快照中的更改。

它还可以选择性地接受一个文件名（`--diff <FILE_NAME>`），默认值为 `.gas-snapshot`。

例如：

```ignore
$ forge snapshot --diff .gas-snapshot2

Running 10 tests for src/test/ERC20.t.sol:ERC20Test
[PASS] testApprove() (gas: 31162)
[PASS] testBurn() (gas: 59875)
[PASS] testFailTransferFromInsufficientAllowance() (gas: 81034)
[PASS] testFailTransferFromInsufficientBalance() (gas: 81662)
[PASS] testFailTransferInsufficientBalance() (gas: 52882)
[PASS] testInfiniteApproveTransferFrom() (gas: 90167)
[PASS] testMetadata() (gas: 14606)
[PASS] testMint() (gas: 53830)
[PASS] testTransfer() (gas: 60473)
[PASS] testTransferFrom() (gas: 84152)
Test result: ok. 10 passed; 0 failed; finished in 2.86ms
testBurn() (gas: 0 (0.000%))
testFailTransferFromInsufficientAllowance() (gas: 0 (0.000%))
testFailTransferFromInsufficientBalance() (gas: 0 (0.000%))
testFailTransferInsufficientBalance() (gas: 0 (0.000%))
testInfiniteApproveTransferFrom() (gas: 0 (0.000%))
testMetadata() (gas: 0 (0.000%))
testMint() (gas: 0 (0.000%))
testTransfer() (gas: 0 (0.000%))
testTransferFrom() (gas: 0 (0.000%))
testApprove() (gas: -8 (-0.000%))
Overall gas change: -8 (-0.000%)
```

`--check` 将比较快照与现有快照文件，并显示所有差异（如果有）。你可以通过提供不同的文件名来更改要比较的文件：`--check <FILE_NAME>`。

例如：

```ignore
$ forge snapshot --check .gas-snapshot2

Running 10 tests for src/test/ERC20.t.sol:ERC20Test
[PASS] testApprove() (gas: 31162)
[PASS] testBurn() (gas: 59875)
[PASS] testFailTransferFromInsufficientAllowance() (gas: 81034)
[PASS] testFailTransferFromInsufficientBalance() (gas: 81662)
[PASS] testFailTransferInsufficientBalance() (gas: 52882)
[PASS] testInfiniteApproveTransferFrom() (gas: 90167)
[PASS] testMetadata() (gas: 14606)
[PASS] testMint() (gas: 53830)
[PASS] testTransfer() (gas: 60473)
[PASS] testTransferFrom() (gas: 84152)
Test result: ok. 10 passed; 0 failed; finished in 2.47ms
Diff in "ERC20Test::testApprove()": consumed "(gas: 31162)" gas, expected "(gas: 31170)" gas 
```