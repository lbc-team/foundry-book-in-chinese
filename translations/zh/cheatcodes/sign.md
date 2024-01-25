## `sign`

### 签名

```solidity
function sign(uint256 privateKey, bytes32 digest) external returns (uint8 v, bytes32 r, bytes32 s);
```

```solidity
function sign(Wallet memory wallet, bytes32 digest) external returns (uint8 v, bytes32 r, bytes32 s);
```

### 描述

使用私钥 `privateKey` 或[Wallet](./create-wallet.md`) `wallet` 对摘要 `digest` 进行签名，返回 `(v, r, s)`。

这对于测试需要签名数据并执行 `ecrecover` 以验证签署者的函数非常有用。

### 示例

```solidity
(address alice, uint256 alicePk) = makeAddrAndKey("alice");
emit log_address(alice);
bytes32 hash = keccak256("Signed by Alice");
(uint8 v, bytes32 r, bytes32 s) = vm.sign(alicePk, hash);
address signer = ecrecover(hash, v, r, s);
assertEq(alice, signer); // [PASS]
```

这对于测试需要签名的函数非常有用：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.17;

import { ECDSA } from "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

contract SigningExample is Ownable {
    using ECDSA for bytes32;

    address public systemAddress;

    function setSystemAddress(address _address) external onlyOwner {
        systemAddress = _address;
    }

    function purchase(uint256 _amount, string calldata _nonce, bytes calldata _signature) external payable {
        require(isValidSignature(
            systemAddress,
            keccak256(abi.encodePacked(msg.sender, _amount, _nonce)),
            _signature
            ), "Invalid Signature"
        );

        // mint tokens
    }

    function isValidSignature(address _systemAddress, bytes32 hash, bytes memory signature) internal view returns (bool) {
        require(_systemAddress != address(0), "Missing System Address");

        bytes32 signedHash = hash.toEthSignedMessageHash();
        return signedHash.recover(signature) == _systemAddress;
    }

}

contract SigningExampleTest is Test {
    using ECDSA for bytes32;

    SigningExample public signingExample;

    uint256 internal userPrivateKey;
    uint256 internal signerPrivateKey;

    function setUp() public {
        signingExample = new SigningExample();

        userPrivateKey = 0xa11ce;
        signerPrivateKey = 0xabc123;

        address signer = vm.addr(signerPrivateKey);
        signingExample.setSystemAddress(signer);
    }

    function testPurchase() public {
        address user = vm.addr(userPrivateKey);
        address signer = vm.addr(signerPrivateKey);

        uint256 amount = 2;
        string memory nonce = 'QSfd8gQE4WYzO29';

        vm.startPrank(signer);
        bytes32 digest = keccak256(abi.encodePacked(user, amount, nonce)).toEthSignedMessageHash();
        (uint8 v, bytes32 r, bytes32 s) = vm.sign(signerPrivateKey, digest);
        bytes memory signature = abi.encodePacked(r, s, v); // note the order here is different from line above.
        vm.stopPrank();

        vm.startPrank(user);
        // Give the user some ETH, just for good measure
        vm.deal(user, 1 ether);

        signingExample.purchase(
            amount,
            nonce,
            signature
        );
        vm.stopPrank();
    }
}
```

#### `Wallet`

`Wallet` 重载是一个简单的包装器，使用钱包的私钥对摘要进行签名

```solidity
Wallet memory alice = vm.createWallet("alice");
bytes32 hash = keccak256("Signed by Alice");
(uint8 v, bytes32 r, bytes32 s) = vm.sign(alice, hash);
address signer = ecrecover(hash, v, r, s);
assertEq(alice.addr, signer); // [PASS]
```
