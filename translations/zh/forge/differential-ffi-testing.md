## 差异测试

Forge 可用于差异测试（differential testing）和差异模糊测试（differential fuzzing）。 您甚至可以使用[ `ffi` 作弊码 ](../cheatcodes/ffi.md) 针对非 EVM 可执行文件进行测试。

### 背景

[差异测试（Differential testing）](https://en.wikipedia.org/wiki/Differential_testing) 通过比较每个实现的输出来交叉引用同一功能的多个实现。 假设我们有一个函数规范 `F(X)`，以及该规范的两个实现：`f1(X)` 和`f2(X)`。 对于存在于适当输入空间中的所有 x，我们期望 `f1(x) == f2(x)`。 如果 `f1(x) != f2(x)`，我们知道至少有一个函数错误地实现了 `F(X)`。 这种测试等效和识别差异的过程是差异测试的核心。

差异模糊测试是差异测试的扩展。 差异模糊以编程方式生成许多`x`值，以发现手动选择的输入可能无法揭示的差异和边缘情况。

> 注意：本例中的 `==` 运算符可以是等效性的自定义定义。例如，如果测试浮点实现，您可以使用具有一定公差的近似相等。

这种类型的测试在现实生活中的一些用途包括：

- 将升级后的实施与其之前版本进行比较
- 针对已知参考实现测试代码
- 确认与第三方工具和依赖项的兼容性

以下是 Forge 如何用于差异测试的一些示例。

### 入门：`ffi`  作弊码

[`ffi`](../cheatcodes/ffi.md) 允许您执行任意 shell 命令并捕获输出。 这是一个模拟示例：

```solidity
import "forge-std/Test.sol";

contract TestContract is Test {

    function testMyFFI () public {
        string[] memory cmds = new string[](2);
        cmds[0] = "cat";
        cmds[1] = "address.txt"; // assume contains abi-encoded address.
        bytes memory result = vm.ffi(cmds);
        address loadedAddress = abi.decode(result, (address));
        // Do something with the address
        // ...
    }
}
```

一个地址之前已经写入了`address.txt`，我们使用 FFI 作弊码读取了它。 现在可以在整个测试合约中使用此数据。

### 示例：差异测试默克尔树实现

[默克尔树](https://en.wikipedia.org/wiki/Merkle_tree) 是区块链应用程序中经常使用的加密承诺方案。 它们的流行导致了默克尔树生成器、证明器和验证器的许多不同实现。 Merkle 根和证明通常使用 JavaScript 或 Python 等语言生成，而证明验证通常发生在 Solidity 的链上。

[Murky](https://github.com/dmfxyz/murky) 是在 Solidity 中 Merkle 根、证明和验证的完整实现。 它的测试套件包括针对 OpenZeppelin 的 Merkle 证明库的差异测试，以及针对参考 JavaScript 实现的根生成测试。 这些测试由 Foundry 的模糊测试和`ffi`功能提供支持。

#### 差异模糊测试参考 TypeScript 实现

使用 `ffi` 作弊代码，Murky 使用 Forge 的模糊器提供的数据对比 TypeScript 实现测试自己的 Merkle 根实现：

```solidity
function testMerkleRootMatchesJSImplementationFuzzed(bytes32[] memory leaves) public {
    vm.assume(leaves.length > 1);
    bytes memory packed = abi.encodePacked(leaves);
    string[] memory runJsInputs = new string[](8);

    // Build ffi command string
    runJsInputs[0] = 'npm';
    runJsInputs[1] = '--prefix';
    runJsInputs[2] = 'differential_testing/scripts/';
    runJsInputs[3] = '--silent';
    runJsInputs[4] = 'run';
    runJsInputs[5] = 'generate-root-cli';
    runJsInputs[6] = leaves.length.toString();
    runJsInputs[7] = packed.toHexString();

    // Run command and capture output
    bytes memory jsResult = vm.ffi(runJsInputs);
    bytes32 jsGeneratedRoot = abi.decode(jsResult, (bytes32));

    // Calculate root using Murky
    bytes32 murkyGeneratedRoot = m.getRoot(leaves);
    assertEq(murkyGeneratedRoot, jsGeneratedRoot);
}
```

> 注意：请参阅 Murky Repo 中的 [`Strings2.sol`](https://github.com/dmfxyz/murky/blob/main/differential_testing/test/utils/Strings2.sol) 以了解启用 `(bytes memory).toHexString()`

Forge 运行 `npm --prefix differential_testing/scripts/ --silent run generate-root-cli {numLeaves} {hexEncodedLeaves}`。 这使用参考 JavaScript 实现计算输入数据的 Merkle 根。 该脚本将根打印到标准输出，打印输出在 `vm.ffi()` 的返回值中被捕获为 `bytes`。

然后测试使用 Solidity 实现计算根。

最后，测试断言两个根完全相等。 如果它们不相等，则测试失败。

#### 针对 OpenZeppelin 的 Merkle 证明库的差异模糊测试

您可能希望对另一个 Solidity 实现使用差异测试。 在这种情况下，不需要 `ffi`。 相反，只需要把参考实现直接导入到测试中。

```solidity
import "openzeppelin-contracts/contracts/utils/cryptography/MerkleProof.sol";
//...
function testCompatibilityOpenZeppelinProver(bytes32[] memory _data, uint256 node) public {
    vm.assume(_data.length > 1);
    vm.assume(node < _data.length);
    bytes32 root = m.getRoot(_data);
    bytes32[] memory proof = m.getProof(_data, node);
    bytes32 valueToProve = _data[node];
    bool murkyVerified = m.verifyProof(root, proof, valueToProve);
    bool ozVerified = MerkleProof.verify(proof, root, valueToProve);
    assertTrue(murkyVerified == ozVerified);
}
```

#### 针对已知边缘情况的差异测试

差异测试并不总是模糊的——它们对于测试已知的边缘情况也很有用。 在 Murky 代码库的情况下，`log2ceil` 函数的初始实现不适用于某些紧挨 2 的幂的长度（如 129）的数组。 作为安全检查，始终针对此长度的数组运行测试，并与 TypeScript 实现进行比较。 您可以在 [此处](https://github.com/dmfxyz/murky/blob/main/differential_testing/test/DifferentialTests.t.sol#L21) 查看完整测试。

#### 针对参考数据的标准化测试

FFI 还可用于将可重现的标准化数据注入测试环境。 在 Murky 库中，这被用作 Gas 快照的基准（参见 [forge 快照](./gas-snapshots.md)）。

```solidity
bytes32[100] data;
uint256[8] leaves = [4, 8, 15, 16, 23, 42, 69, 88];

function setUp() public {
    string[] memory inputs = new string[](2);
    inputs[0] = "cat";
    inputs[1] = "src/test/standard_data/StandardInput.txt";
    bytes memory result =  vm.ffi(inputs);
    data = abi.decode(result, (bytes32[100]));
    m = new Merkle();
}

function testMerkleGenerateProofStandard() public view {
    bytes32[] memory _data = _getData();
    for (uint i = 0; i < leaves.length; ++i) {
        m.getProof(_data, leaves[i]);
    }
}
```

`src/test/standard_data/StandardInput.txt` 是一个包含编码的 `bytes32[100]` 数组的文本文件。 它是在测试之外生成的，可以在任何语言的 Web3 SDK 中使用。 它看起来像：

```ignore
0xf910ccaa307836354233316666386231414464306335333243453944383735313..423532
```

标准化测试合约使用 ffi 读取文件。 它将数据解码为一个数组，然后在本例中为 8 个不同的叶子生成证明。 由于数据是恒定且标准的，我们可以使用此测试有意义地测量 Gas 和性能改进。

> 当然，可以将数组硬编码到测试中！ 但这使得跨合约、实现等进行一致的测试变得更加困难。

### 示例：差异测试渐进式荷兰拍卖

Paradigm 的 [渐进式荷兰拍卖（Gradual Dutch Auction）](https://www.paradigm.xyz/2022/04/gda) 机制的参考实现包含许多差异化的模糊测试。 它是一个很好的存储库，可以进一步探索使用 `ffi` 的差异测试。

- 针对 [Discrete GDAs ](https://github.com/FrankieIsLost/gradual-dutch-auction/blob/master/src/test/DiscreteGDA.t.sol#L78)的差异测试
- 针对  [Continuous GDAs](https://github.com/FrankieIsLost/gradual-dutch-auction/blob/master/src/test/ContinuousGDA.t.sol#L89)的差异测试
- 参考 [Python 实现](https://github.com/FrankieIsLost/gradual-dutch-auction/blob/master/analysis/compute_price.py)

### 参考资料库

- [Gradual Dutch Auctions](https://github.com/FrankieIsLost/gradual-dutch-auction)
- [Murky](https://www.github.com/dmfxyz/murky)
- [Solidity Fuzzing Template](https://github.com/patrickd-/solidity-fuzzing-boilerplate)

如果您有另一个可以作为参考的存储库，请贡献它！
