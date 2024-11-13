## Struct 编码

Struct 是用户定义的类型，可以将多个变量组合在一起：

```solidity
struct MyStruct {
    address addr;
    uint256 amount;
}
```

只有新的 [ABI 编码器 v2](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#abi-coder-pragma) 能够对任意嵌套的数组和结构体进行编码和解码。从 Solidity 0.8.0 开始默认激活，在此之前需要通过 `pragma experimental ABIEncoderV2` 激活。

Solidity 结构体映射到 ABI 类型 “tuple”。有关 Solidity 类型如何映射到 ABI 类型的更多信息，请参见 Solidity 文档中的 [Mapping Solidity to ABI types](https://docs.soliditylang.org/en/latest/abi-spec.html#mapping-solidity-to-abi-types)。

因此，结构体会被编码和解码为元组。因此，我们上面定义的结构体 `MyStruct`，在 ABI 中映射为元组 `(address,uint256)`。

让我们看看这在合约中的工作原理：

```solidity
pragma solidity =0.8.15;

contract Test {
    struct MyStruct {
        address addr;
        uint256 amount;
    }

    function f(MyStruct memory t) public pure {}
}
```

这个合约中 `f` 函数的 ABI 是：

```json
{
	"inputs": [
		{
			"components": [
				{
					"internalType": "address",
					"name": "addr",
					"type": "address"
				},
				{
					"internalType": "uint256",
					"name": "amount",
					"type": "uint256"
				}
			],
			"internalType": "struct Test.MyStruct",
			"name": "t",
			"type": "tuple"
		}
	],
	"name": "f",
	"outputs": [],
	"stateMutability": "pure",
	"type": "function"
}
```

该函数 `f` 接受一个类型为 `tuple` 的输入，包含两个组件，分别为 `address` 类型和 `uint256` 类型。

**嵌套结构体编码：**
这是一个更复杂的嵌套结构体示例：

```solidity
pragma solidity 0.8.21;

contract Test {
    struct nestedStruct {
        address addr;
        uint256 amount;
    }

    struct MyStruct {
        string nestedStructName;
        uint256 nestedStructCount;
        nestedStruct _nestedStruct;
    }

    function f(MyStruct memory t) public pure {}
}
```

此合约中 `f` 函数的 ABI 为：

```json
{
    "inputs": [
        {
            "name": "t",
            "type": "tuple",
            "internalType": "struct Test.MyStruct",
            "components": [
                {
                    "name": "nestedStructName",
                    "type": "string",
                    "internalType": "string"
                },
                {
                    "name": "nestedStructCount",
                    "type": "uint256",
                    "internalType": "uint256"
                },
                {
                    "name": "_nestedStruct",
                    "type": "tuple",
                    "internalType": "struct Test.nestedStruct",
                    "components": [
                        {
                            "name": "addr",
                            "type": "address",
                            "internalType": "address"
                        },
                        {
                            "name": "amount",
                            "type": "uint256",
                            "internalType": "uint256"
                        }
                    ]
                }
            ]
        }
    ],
    "name": "f",
    "outputs": [],
    "stateMutability": "pure",
    "type": "function"
}
```

其含义为：函数 `f` 接受 1 个类型为 tuple 的输入，该 tuple 有三个组件：一个字符串，一个 uint256，以及另一个表示嵌套结构体的 tuple，其组件为类型为 address 的 addr 和类型为 uint256 的 amount。

将 `MyStruct` 编码为参数传递给函数 `f`：
```bash
cast abi-encode "f((string,uint256,(address,uint256)))" "(example,1,(0x...,1))"
```

部署一个接受 `MyStruct` 作为参数的合约：
```bash
forge create src/Test.sol:Test --constructor-args "(example,1,(0x...,1))"
```