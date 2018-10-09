# Consensus

## BFT

## Use

Byzantine Faults are the most severe and difficult to deal with. Byzantine Fault Tolerance has been needed in airplane engine systems, nuclear power plants and pretty much any system whose actions depend on the results of a large amount of sensors. 

In BFT, validators sends messages back and forth and use a voting process where a new ledger is confirmed if over 66% of validators(preset by a central authority, the company behinde the protocol) agree on that ledger. It is cheaper and faster than POW but sacrifies decentralization to achieve those features. 

## FBA

Federated Byzentine agreement

> FBA = BFT + POW

BFT is centralise and closed member ship system as validators list is managed by one central authority, usually the company behind the protocol. But in FBA there no recommended validator list by any authority, rather each validator can decide which other validators they trust and the list of trusted validator is called **Quorum slice**.

**Quorum slice** of each validator may overlap to form quorum or network wide consensus of a tranasaction.

> Open membership -> Decentralization

  

## How related to BC?

Blockchains are decentralized ledgers which, by definition, are not controlled by a central authority. 

Due to the value stored in these ledgers, bad actors have huge economic incentives to try and cause faults.

In the absence of BFT, a peer is able to transmit and post false transactions effectively nullifying the blockchain’s reliability. To make things worse, there is no central authority to take over and repair the damage.

**Proof-of-Work** is a probabilistic solution to the Byzantine Generals Problem

> The blockchain is a general solution to the Byzantine Generals’ Problem. 

- https://medium.com/loom-network/understanding-blockchain-fundamentals-part-1-byzantine-fault-tolerance-245f46fe8419 
- https://marknelson.us/posts/2007/07/23/byzantine.html
- https://www.mail-archive.com/cryptography@metzdowd.com/msg09997.html 
