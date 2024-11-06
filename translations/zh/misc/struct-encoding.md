## Struct Encoding

Structs are user defined types that can group several variables:

```solidity
struct MyStruct {
    address addr;
    uint256 amount;
}
```

Only the new [ABI coder v2](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#abi-coder-pragma) can encode and decode arbitrarily nested arrays and structs. Since Solidity 0.8.0 it is activated by default, prior to that it needs to be activated via `pragma experimental ABIEncoderV2`.

Solidity structs map to the ABI type "tuple". For more information on how Solidity types map to ABI types see [Mapping Solidity to ABI types](https://docs.soliditylang.org/en/latest/abi-spec.html#mapping-solidity-to-abi-types) in the Solidity documentation.

Structs are therefore encoded and decodes as tuples. So the struct we defined above, `MyStruct`, maps to the tuple `(address,uint256)` in terms of the ABI.

Let's see how this works in a contract:

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

The ABI of the `f` function in this contract is:

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

which reads: The function `f` takes 1 input of type `tuple` with two components of type `address` and `uint256`.

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