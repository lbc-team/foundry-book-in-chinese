## `getDeployedCode`

### 签名

```solidity
function getDeployedCode(string calldata) external returns (bytes memory);
```

### 描述

此作弊码类似于 [`getCode`](./get-code.md)，但仅返回项目中合约的 **部署** 字节码（也称为运行时字节码）。

此作弊码的主要用例是将无状态合约快速部署到任意地址。

calldata 参数可以是 `ContractFile.sol`（如果文件名和合约名相同），`ContractFile.sol:ContractName`，或者是相对于项目根目录的工件路径。

> ℹ️ **注意**
>
> `getDeployedCode` 需要输出目录的读取权限，请参阅 [文件作弊码](./fs.md) 。
>
> 要授予读取访问权限，请在您的 `foundry.toml` 中设置 `fs_permissions = [{ access = "read", path = "./out"}]`。

### 例子

使用 `getDeployedCode` 和 [`etch`](./etch.md) 在任意地址部署无状态合约。

```solidity
// A stateless contract that we want deployed at a specific address
contract Override {
    event Payload(address sender, address target, bytes data);

    function emitPayload(address target, bytes calldata message) external payable returns (uint256) {
        emit Payload(msg.sender, target, message);
        return 0;
    }
}

// get the **deployedBytecode**
bytes memory code = vm.getDeployedCode("Override.sol:Override");

// set the code of an arbitrary address
address overrideAddress = address(64);
vm.etch(overrideAddress, code);
assertEq(
    overrideAddress.code,
    code
)
```

### 另请参阅

Forge 标准库

[`getCode`](./get-code.md)
[`etch`](./etch.md)

[forge-std]: ../reference/forge-std
