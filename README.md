# Web3 Learning Journal
## A place to jot down what I learned for the day



[Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)

---

### 12/9/2022 Hashcash

Hashcash implements SHA-1 to reduce spam emails. Mozilla and Microsoft once utilized this method. The idea was to create a proof of work for an email so sending many emails would be CPU intensive. Hashcash was the inspiration for Satoshi Nakamoto's Bitcoin. 

---

### 12/6/2022 Diffie-Hellman Exchange Key

[Video explanation.](https://www.youtube.com/watch?v=NmM9HA2MQGI) Used often in TLS handshake in HTTPS.

---

### 12/5/2022 Public Key Cryptography

The ECDSA (Elliptic Curve Digital Signature Algorithm) secp256k1 is used by both Ethereum and Bitcoin. Wallet addresses are generated from the public key derived from secp256k1. For Bitcoin, it involves a [checksum and base58 check](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses) and for Ethereum, it is the last 20 bytes of the hash. Base58 is to avoid confusing characters like 0 and O.

---

### 12/1/2022 Layer 2s cont.

![Data in L2s](https://i.imgur.com/TWmjV4H.png)

* Volitions

  Rollup and Validium hybrid. 

---

### 11/30/2022 Layer 2s cont.

* Plasma
  
  "A Plasma chain is a separate blockchain anchored to Ethereum Mainnet but executing transactions off-chain with its own mechanism for block validation. Plasma chains are sometimes referred to as "child" chains, essentially smaller copies of the Ethereum Mainnet. Plasma chains use fraud proofs (like optimistic rollups) to arbitrate disputes. Merkle trees enable the creation of an endless stack of these chains that can work to offload bandwidth from parent chains." [Ethereum.org](https://ethereum.org/en/developers/docs/scaling/plasma/)

* Validiums

  Enforces integrity of transactions using validity proofs. Executes transactions off-chain.




---

### 11/29/2022 Layer 2s

* State/Payment Channels

  Two or more users lock up funds in a contract and transact amongst each other in a trusted small circle. Only the end state is saved onto the chain.
  
* Side Chains

  Not technically a Layer 2. Runs parallel with Layer 1 but has own governance consensus with validators.
  
* Rollups

  Bundle up transactions and send as a batch.

* Optimistic Rollups (ORs)

  Batch transactions are valid by default. Transaction proposer (asserters) stake ETH and provide bond. Users can challenge authenticity. Verifiers check disputes and whoever is right gets rewarded.

* Zero-Knowledge Rollups (ZKRs)

  Batch transactions are bundled with a proof for authenticity. Can be expensive.  

Unspent transaction output (UTXO) is the technical term for the amount of digital currency that remains after a cryptocurrency transaction.

---

### 11/28/2022 More Gas Optimizations

[1](https://mudit.blog/solidity-gas-optimization-tips/)

[2](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/)

---

### 11/27/2022 Proxy Pattern and Meta Transactions

Contracts are upgradable through proxy design patterns. 

Gas-less transactions are achieveable through meta transactions. e.g. In OpenSea, a seller is able to put up an NFT for sale and the buyer pays for the NFT and the transaction costs. 

Elliptic Curve Digital Signature Algorithm (ECDSA) is the signatures algorithm used by Ethereum and OpenZeppelin.

Ways to trigger the Fallback function:

1. Calling a function that doesn’t exist inside the contract, or
2. Calling a function without passing in required data, or
3. Sending Ether without any data to the contract



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
