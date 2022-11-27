# Web Learning Journal
## A place to jot down what I learned for the day



[Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)

---




### Proxy Pattern and Meta Transactions

Contracts are upgradable through proxy design patterns. 

Gas-less transactions are achieveable through meta transactions. e.g. In OpenSea, a seller is able to put up an NFT for sale and the buyer pays for the NFT and the transaction costs. 

Elliptic Curve Digital Signature Algorithm (ECDSA) is the signatures algorithm used by Ethereum and OpenZeppelin.



---


### 11/26/2022 delegatecall() and Reentrancy Vulnerabilities
Created a demo of vulnerabilities in smart contracts. [Link](https://github.com/podoodoo/LW3DAO/tree/master/04-Senior/reentrancy) [Link](https://github.com/podoodoo/LW3DAO/tree/master/04-Senior/delegatecall)

Using ethers.provider.getStorageAt(), it's possible to read stack memory in blocks. Even private memory.

msg.sender is generally more superior to tx.origin. 

Function modifiers use the same stack as the function on which they are put and counts as 1 of the 16 variables.

---

### 11/25/2022 Gas optimizations, Flash Loans
Some solidity gas optimization techniques/notes:
- Packing variables in storage into 256 bit sizes. 
- SSTORE and SSLOAD are expensive OPCODEs so its more cost effective to save local variables. 
- "Constant variables are replaced at compile time by their values, while immutable variables are replaced at deployment time... constant variables cannot make reference to the state of the blockchain nor call external contracts" (ie. block.number, address(this).balance, msg.value, gasleft()). 
- Maps are usually more gas efficient because arrays include mapping with checking
```
struct Array{
  mapping(uint => someType) items;
  uint length;
}
```
Learned how to use Aave to create flash loan contracts and test with a forked polygon mainnet in hardhat. [Link](https://github.com/podoodoo/LW3DAO/tree/master/04-Senior/flashloans)

---

### 11/24/2022 Ethereum Storage and Execution 
World state is a map of addresses and account states. Contract accounts are comprised of balance, nonce, storage hash, and code hash. And Externally Owned Accounts (EOA) (ie. MetaMask or Coinbase wallets) just have balance and nonce. 

There are two types of transactions: contract creation and sending messages. Sending messages are for ETH transfers and/or function calls.

EVM stack sizes are 32 bytes (256 bits) x 1024 and can only store 16 local variables. 

Memory can only be read in 32 byte chunks. Similar to WORDs in CPUs.  

---

### 11/23/2022 Finished Junior course in LearnWeb3DAO
Using [The Graph](https://thegraph.com/hosted-service/subgraph/podoodoo/learnweb3) to track events on [RandomWinnerGame](https://mumbai.polygonscan.com/address/0x2968D09a5c4e89E6fCC8f2Dc7d67659C32467ec2) contract. The contract utilizes Chainlink VRF to randomly pick a winner from a pool of entrants. 
