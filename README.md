# Web3 Learning Journal
## A place to jot down what I learned for the day



[Markdown Cheatsheet](https://www.markdownguide.org/cheat-sheet/)

---

### 3/5/2023 Overflow

The general best practice is "multiplication before division" to prevent issues involving loss of precision. Starting Solidity 0.8.0, an overflow happening outside of an "unchecked" block will always result in the transaction reverting.

---

### 1/31/2023 Ethernaut #6

Alien Codex - when child contract has a variable in the same slot as a parent variable, the compiler tries to compact them into the same slot. Ex. Contract B is A and slot 0s in contract A { bool a; } and contract B { bool b; }. Bytecode then compiles slot 0 into 0x..aabb because bool = 1 byte.

---

### 1/30/2023 Ethernaut #5

Recovery - Obtain created contract address from etherscan and invoke selfdestruct

---

### 1/16/2023 Checks Effects Interactions

Use the Checks-Effects-Interactions pattern to prevent reentrancy vulnerabilities.

---

### 1/14/2023 Storage slots

`mapping` storage slots are calculated by `keccak(key type + base)`. 

Example: 

```
contract SomeContract {
    int256 a;
    int256 b;
    int256 c;
    mapping(int256=>bool) public map;
    ...
    map[1] = 2;
}
``` 

The storage slot would be calculated as `keccak(1 + 3)`. 

---

### 1/9/2023 Alchemy University Week 7 ERC20

[My transaction](https://goerli.etherscan.io/tx/0x2463b1355291725958a80e4516c2a3bb460ac7f2901b431d2733d43d0c0a65ec) to emit Winner.

---

### 1/8/2023 Multisig

Multisig is a transaction that needs to be signed off by multiple parties. Think of it like a giant wallet with a bit of governance. 

---

### 1/7/2023 Ethernaut #4

Gatekeeper Two - In `gateTwo()`,

```
assembly { x := extcodesize(caller()) }
    require(x == 0);
```

the assembly code is looking for the ["size of the code at address `caller()`"](https://docs.soliditylang.org/en/v0.4.23/assembly.html). `caller()` is the contract address of GateKeeperTwoSolution.sol. When contract is being created, code size (`extcodesize`) is 0 so you just have to put everything inside the `constructor()`.

In `gateThree()`, using the formula (A ^ B = C) == (A ^ C = B), it is simple to figure out the key.

My code:

```
constructor() {
        GatekeeperTwo gate = GatekeeperTwo(0x932cCdEE6d559B40F309F763f6a1d837C596402D);
        bytes8 gateKey = bytes8(uint64(bytes8(keccak256(abi.encodePacked(address(this))))) ^ type(uint64).max);
        gate.enter(gateKey);
}
```

Naught Coin - Just needed to use `approve()` and `transferFrom()` ERC20 methods.

Preservation - What I thought was Delegation all over again was riddle with bugs somehow. I don't know what happened. I still don't know exactly how it worked. 

```
contract PreservationSolution {
    // public library contracts 
    address public timeZone1Library;
    address public timeZone2Library;
    address public owner; 
    uint storedTime;
    // Sets the function signature for delegatecall
    bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

    Preservation preservation = Preservation(0xe208Cb45150Ce7d28ebE2B3643ab004Ebf08eFe3);

    function attack() public {
        preservation.setFirstTime(uint(uint160(address(this))));
        preservation.setFirstTime(1);
    }

    function setTime(uint _time) public {
        owner = msg.sender;
    }
}
```

---

### 1/6/2023 Ethernaut #3

Reentrance - [Bad actor contract](https://goerli.etherscan.io/address/0xeAb3e298d11501f2c625AFa4AD9b65f4753d9457). Reentrancy attack calling withdraw again in `receive()`. 

Elevator - Make a contract "overriding" the method `isLastFloor()` and have it return a toggling boolean. 

Privacy - Similar to Vault with a few extra quick maffs. Read storage slot 5.

Gatekeeper One - Lots of bit manipulation. Calls `enter()` over and over to brute force match `gasleft()`.
```
function enter() public {
        bytes8 gateKey = bytes8(uint64(uint160(tx.origin))) & 0xFFFFFFFF0000FFFF;
        bool s = false;
        uint i = 0;
        while(!s) {
            (s, ) = address(gate).call{ gas: i + (8191 * 5) }(abi.encodeWithSignature("enter(bytes8)", gateKey));
            i += 1;
        }
    }
```

---

### 1/5/2023 Ethernaut #2

King - Create separate denial of service contract that sends sufficient eth to King contract without fallback/receive. Initially, I had `payable(contract).call{ value: eth }("")` and was getting `Warning: Return value of low-level calls not used.` I didn't think I needed to use the return values, but I actually did and was the cause for the transaction getting an out of gas error.

---

### 1/4/2023 Ethernaut #1

Fal1out - The "constructor" was actually just a function you could call to transfer ownership of the sender

Fallback - `recieve()` function transferred ownership if `msg.value > 0 && contributions[msg.sender] > 0` and you could satisfy `contributions[msg.sender] > 0` with `contribute()`

CoinFlip - Block numbers are not randomized in `uint256 blockValue = uint256(blockhash(block.number - 1));`. Call `flip()` from another contract.

Telephone - Satisfy `tx.origin != msg.sender` by calling transaction from another contract.

Token - Setting `_value` to a number greater than the initial 20 causes an underflow in `balances[msg.sender] - _value >= 0`. Solidity version >0.8 forces this to revert. 

Delegation - call `pwn()` with `delegatecall()`. In console call `contract.sendTransaction({ data:"dd365b8b" })`. `d365b8b` is from the first 4 bytes of `pwn()` in keccak256.

Force - transfer funds of another contract through `selfdestruct(address)`

Vault - Read contract storage slot 1 through `eth.getStorageAt()`. Used Alchemy Composer.

---

### 1/3/2023 Reference Types

Use `storage` for persistence on the blockchain, `memory` for temporary storage per transaction, and `calldata` for arguments (also temporary).

`memory` arrays do not have push or pop methods. 

Structs in the ABI are defined as tuples.  

---

### 1/2/2023 Events and Logs

Events and logs are stored on the blockchain in transaction receipts but they are not required for blockchain concensus. They are however verified by the blockchain since transaction receipt hashes are stored inside blocks.

---

### 12/31/2022 selfdestruct()

Correct use: `selfdestruct(payable(address))`

The payable is there because some methods could protect against accidentally sending ether.

[My "Winner" event](https://goerli.etherscan.io/tx/0x3669eb346e8c2258a255ad46d6eca9cbf7709d13d55ae11411d9587cfbf4da48) from Alchemy University Week 4.

---

### 12/30/2022 Turing Complete

JUMP and JUMPI op codes are what makes the EVM Turing Complete. Allows for loops.

---

### 12/28/2022 Node P2P Discovery 

Node peer discovery algorithm is based on the kademlia protocol.

[Stack Exchange link](https://ethereum.stackexchange.com/questions/7743/what-are-the-peer-discovery-mechanisms-involved-in-ethereum) with more details.

---

### 12/26/2022 Ethereum Tries

Trie is pronounced "try."
There are four tries hashes in Ethereum blocks: receipt, state, transaction, and storage trie.

![Ethereum Tries](https://res.cloudinary.com/divzjiip8/image/upload/v1669869958/guides/Screen_Shot_2022-11-30_at_8.45.47_PM.png)

---

### 12/24/2022 Merkles 

Bitcoin nodes store merkle root hashes in [LevelDB](https://dbdb.io/db/leveldb).
Ethereum uses Patricia Merkle Trees which is a combination of Merkle + Radix trees.

---

### 12/21/2022 Getting mapping Values in Struct in Hardhat

You have to create a getter for getting values from mapping in a Struct. Hardhat doesn't recognize mappings in Structs.

---

### 12/19/2022 Getting Struct Values

You can destructure structs to get the values in Solidity. [Link](https://ethereum.stackexchange.com/a/126930/106502)

---

### 12/16/2022 Upgradeable Tokens

When using upgradeable tokens, you can't use constructors and have to use the initialize() method.

---

### 12/14/2022 Solidity Array

Arrays always start at a new slot of storage

---

### 12/12/2022 UTXO

Bitcoin uses UTXO model to keep track of user and state/balances and Ethereum uses the Account model. UTXO in Bitcoin is like fake credits that allows you to do transactions but you don't really own it like Groupon or Amazon credits. UTXO is stateless.

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
Created a demo of vulnerabilities in smart contracts. [Link1](https://github.com/podoodoo/LW3DAO/tree/master/04-Senior/reentrancy) [Link2](https://github.com/podoodoo/LW3DAO/tree/master/04-Senior/delegatecall)

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
