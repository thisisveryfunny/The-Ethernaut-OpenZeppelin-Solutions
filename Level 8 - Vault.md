### Contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
    bool public locked;
    bytes32 private password;

    constructor(bytes32 _password) {
        locked = true;
        password = _password;
    }

    function unlock(bytes32 _password) public {
        if (password == _password) {
            locked = false;
        }
    }
}
```

I need to unlock the vault using the password, but i don't know the password.

But with Smart contract, private doesn't mean private lol.
I can simply access the private variables that are in each slots of a smart contract. 
The password is stored in the slot 1.

```javascript
await web3.eth.getStorageAt(contract.address, 1) // (address, slot_number)
```

### Thing that helped me
https://www.youtube.com/watch?v=Gg6nt3YW74o
