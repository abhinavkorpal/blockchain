# blockchain
Note


What is P2PK, P2PKH, P2SH, P2WPKH ?

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
