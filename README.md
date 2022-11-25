# Web Learning Journal
## A place to jot down what I learned for the day

---

[Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)

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
Learned how to use Aave to create flash loan contracts and test with a forked polygon mainnet in hardhat.

---

### 11/24/2022 Ethereum Storage and Execution 
World state is a map of addresses and account states. Contract accounts are comprised of balance, nonce, storage hash, and code hash. And Externally Owned Accounts (EOA) (ie. MetaMask or Coinbase wallets) just have balance and nonce. 
There are two types of transactions: contract creation and sending messages. Sending messages are for ETH transfers and/or function calls.
EVM stack sizes are 32 bytes (256 bits) x 1024 and can only store 16 local variables. 
Memory can only be read in 32 byte chunks. Similar to WORDs in CPUs.  

---

### 11/23/2022 Finished Junior course in LearnWeb3DAO
Using [The Graph](https://thegraph.com/hosted-service/subgraph/podoodoo/learnweb3) to track events on [RandomWinnerGame](https://mumbai.polygonscan.com/address/0x2968D09a5c4e89E6fCC8f2Dc7d67659C32467ec2) contract. The contract utilizes Chainlink VRF to randomly pick a winner from a pool of entrants. 
