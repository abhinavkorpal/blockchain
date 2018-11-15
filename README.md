# blockchain
Notes


### What is P2PK, P2PKH, P2SH, P2WPKH ?

P2PKH: "Pay To Public Key Hash"
This is how transactions are made. You are requiring the sender to supply a valid signature (from the private key) and public key. The transaction output script will use the signature and public key and through some cryptographic functions will check if it matches with the public key hash, if it does, then the funds will be spendable. This method conceals your public key in the form of a hash for extra security.

P2PK: "Pay To Public Key"
This is similar to P2PKH; the difference is that it does not conceal your public key. Anyone using this method to send funds over the P2P network is showing people their public key in the transaction details.

P2SH: "Pay To Script Hash"
The outputs of a transaction are just scripts that, if are executed with specific parameters, will result in a boolean of true or false. If a miner runs the output script with the supplied parameters and results in true, the money will be sent to your desired output. P2SH is used for multi-signature wallets making the output scripts logic that checks for multiple signatures before accepting the transaction. P2SH can also be used to allow anyone, or no one, to spend the funds. If the output script of a P2SH transaction is just 1 for true, then attempting to spend the output without supplying parameters will just result in 1 making the money spendable by anyone who tries. This also applies to scripts that return 0, making the output spendable by no one.

This can also be used for puzzles like this one.

P2WPKH: "Pay To Witness Public Key Hash"
This was a feature of segwit which stands for Segregated Witness. Instead of using scriptSig parameters to check the transaction validity, there is a new part of the transaction called witness where the validity occurs.

Also what is the difference between BTC held in P2SH and Unspent P2SH Output here?

This means that out of all the P2SH transactions (transactions that can be spent by anyone with a set of parameters [scriptSig] that results in the execution of scriptPubkey with true), the ones with unspent outputs have not been redeemed.

I recommend this video by Andreas Antonopoulos on Bitcoin scripting.

### Smart contracts
As we discussed earlier, there are two different types of account in Ethereum: Externally Owned Accounts (EOAs) and contract accounts. EOAs are controlled by users, often via software, such as a wallet application, that are external to the Ethereum platform. In contrast to that, contract accounts are controlled by their program code (also commonly referred to as smart contracts) that is executed by the Ethereum Virtual Machine (EVM).

In short:

EOA are simple accounts without any associated code or data storage, whereas contract accounts have both associated code and data storage. 

EOAs are controlled by transactions created and cryptographically signed with a private key in the "real world" external to and independent of the protocol, whereas contract accounts do not have private keys and so "control themselves" in the predetermined way prescribed by their smart contract code.

Both types of accounts are identified by an Ethereum address.
In this section, we will discuss contract accounts, and the program code that controls the contract accounts: smart contracts.

### What is a smart contract?

The term smart contract has been used over the years to describe a wide variety of different things. In the 1990’s, cryptographer Nick Szabo coined the term and defined it as “a set of promises, specified in digital form, including protocols within which the parties perform on the other promises”. Since then, the concept of smart contracts has evolved, especially after the introduction of decentralized blockchain platforms with the invention of Bitcoin in 2009.

In the context of Ethereum, the term is actually a bit of a misnomer, given that Ethereum smart contracts are neither smart nor legal contracts, but the term has stuck.

In this notebook, we use the term “smart contract” to refer to immutable computer programs that run deterministically in the context of an Ethereum Virtual Machine as part of the Ethereum network protocol, i.e. on the decentralized Ethereum world computer.

Let’s unpack that definition:

Computer programs: Smart contracts are simply computer programs. The word contract has no legal meaning in this context.


Immutable: Once deployed, the code of a smart contract cannot change. Unlike traditional software, the only way to modify a smart contract is to deploy a new instance.


Deterministic: The outcome of the execution of a smart contract is the same for everyone who runs it, given the context of the transaction that initiated its execution and the state of the Ethereum blockchain at the moment of execution.


The EVM context: Smart contracts operate with a very limited execution context. They can access their own state, the context of the transaction that called them and some information about the most recent blocks.


Decentralized world computer: The EVM runs as a local instance on every Ethereum node, but because all instances of the EVM operate on the same initial state and produce the same final state, the system as a whole operates as a single "world computer".

### Lifecycle of a smart contract

Smart contracts are typically written in a high-level language, such as Solidity. But in order to run, they must be compiled to the low-level bytecode that runs in the EVM. Once compiled, they are deployed on the Ethereum platform using a special contract creation transaction which is identified as such by being sent to the special contract creation address, namely 0x0.

Each contract is identified by an Ethereum address, which is derived from the contract creation transaction as a function of the originating account and nonce. The Ethereum address of a contract can be used in a transaction as the recipient, sending funds to the contract or calling one of the contract’s functions. Note that, unlike EOAs, there are no keys associated with an account created for a new smart contract. As the contract creator, you don’t get any special privileges at the protocol level (although you can explicitly code them into the smart contract, of course). You certainly don’t receive the private key for the contract account - it does’t exist - we can say that smart contract accounts own themselves.

Importantly, contracts only run if they are called by a transaction. All smart contracts in Ethereum are executed, ultimately, because of a transaction initiated from an Externally Owned Account. A contract can call another contract that can call another contract, and so on, but the first contract in such a chain of execution will always have been called by a transaction from an EOA. Contracts never run “on their own”, or “run in the background”. Contracts effectively lie “dormant” until a transaction triggers execution, either directly or indirectly as part of a chain of contract calls.

It is also worth noting that smart contracts are not executed "in parallel" in any sense - the Ethereum world computer can be considered to be a single-threaded machine.

Transactions are atomic, regardless of how many contracts they call or what those contracts do when called. Transactions execute in their entirety, with any changes in the global state (contracts, accounts, etc.) recorded only if all execution terminates successfully. Successful termination means that the program executed without an error and reached the end of execution. If execution fails due to an error, all of its effects (changes in state) are “rolled back” as if the transaction never ran. A failed transaction is still recorded as having been attempted, and the ether spent on gas for the execution is deducted from the originating account, but it otherwise has no other effects on contract or account state.

As mentioned above, it important to remember that a contract’s code cannot be changed. However a contract can be “deleted”, removing the code and it’s internal state (storage) from its address, leaving a blank account. Any transactions sent to that account address after the contract has been deleted do not result in any code execution, because there is no longer any code there to execute. To delete a contract, you execute an EVM opcode called SELFDESTRUCT (previously called SUICIDE). That operation costs “negative gas”, a gas refund, thereby incentivizing the release of network client resources from the deletion of stored state. Deleting a contract in this way does not remove the transaction history (past) of the contract, since the blockchain itself is immutable.

It is also important to note that the SELFDESTRUCT capability will only be available if the contract author programmed the smart contract to have that functionality. If the contract’s code does not have a SELFDESTRUCT opcode, or it is inaccessible, the smart contract can not be deleted.
