### Ethernaut Contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force { /*
                   MEOW ?
         /\_/\   /
    ____/ o o \
    /~____  =ø= /
    (______)__m_m)
                   */ 
}
```

Yeah, pretty complex contract. 

The goal is to make this contract get some balance, but a smart contract can't receive Ether if there's no `fallback` function or `receive()` function.

So I need a way to force sending Ether in a contract. 

There is a way to force that using `selfdesctruct()`

To do so, I will simply create a smart contract with a little balance and then selfdestruct it so it's gonna send the remaining funds that are sitting in my freshly created contract into the empty contract. 
### My contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
contract Add {
    address public force;
    constructor (address _force) payable{
        force = _force;
    }
    // Function to call to initiate the destruction
    function destroy(address payable _to) public  {
        // selfdestruct sends all contract ETH to the owner's address
        selfdestruct(_to);
    }
    fallback() external payable { }
    receive() external payable {}
}
```

### Thing that helped me
https://ethereum.stackexchange.com/questions/63987/can-a-contract-with-no-payable-function-have-ether/63988#63988
https://medium.com/@alexsherbuck/two-ways-to-force-ether-into-a-contract-1543c1311c56
https://ethereum.stackexchange.com/questions/63987/can-a-contract-with-no-payable-function-have-ether/63988#63988