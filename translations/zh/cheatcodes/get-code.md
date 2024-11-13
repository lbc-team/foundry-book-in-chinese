## `getCode`

### 签名

```solidity
function getCode(string calldata) external returns (bytes memory);
```

### 描述

根据合约的路径返回项目中合约的 **创建** 字节码。

calldata 参数可以是 `ContractFile.sol`（如果文件名和合约名相同），`ContractFile.sol:ContractName`，或者是相对于项目根目录的工件路径。

> ℹ️ **注意**
>
> `getCode` 需要输出目录的读取权限，请参阅 [文件作弊码](./fs.md) 。
>
> 要授予读取访问权限，请在你的 `foundry.toml` 中设置 `fs_permissions = [{ access = "read", path = "./out"}]`。

### 例子

```solidity
MyContract myContract = new MyContract(arg1, arg2);

// Let's do the same thing with `getCode`
bytes memory args = abi.encode(arg1, arg2);
bytes memory bytecode = abi.encodePacked(vm.getCode("MyContract.sol:MyContract"), args);
address anotherAddress;
assembly {
    anotherAddress := create(0, add(bytecode, 0x20), mload(bytecode))
}

assertEq0(address(myContract).code, anotherAddress.code); // [PASS]
```

通过结合 `getCode` 和 [`etch`](./etch.md) 将合约部署到任意地址

```solidity

// Deploy
bytes memory args = abi.encode(arg1, arg2);
bytes memory bytecode = abi.encodePacked(vm.getCode("MyContract.sol:MyContract"), args);
address deployed;
assembly {
deployed := create(0, add(bytecode, 0x20), mload(bytecode))
}

// Set the bytecode of an arbitrary address
vm.etch(targetAddr, deployed.code);
```


### Supported formats

你可以通过合约路径或合约名称获取构件。也支持获取特定版本的构件。如果未提供版本，cheatcode 将默认为正在执行的测试版本或构件编译时的唯一版本。
```solidity
vm.getCode("MyContract.sol:MyContract");
vm.getCode("MyContract");
vm.getCode("MyContract.sol:0.8.18");
vm.getCode("MyContract:0.8.18");
```

### 另请参阅

[`getDeployedCode`](./get-deployed-code.md)
[`eth`](./etch.md)

Forge 标准库

[`deployCode`](../reference/forge-std/deployCode.md)
[`deployCodeTo`](../reference/forge-std/deployCodeTo.md)

[forge-std]: ../reference/forge-std