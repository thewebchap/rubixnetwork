﻿# Key Features
This segment introduces you to the key features and theoretical concepts behind Rubix network. It is mainly for the users who are willing to explore Rubix architecture in depth. 

> **NOTE** :clipboard: The Features mentioned here are written in Rubix flow order and therefore we suggest you to read in the prescribed order to understand the concepts legibly. 

The following concepts provides an outline of RubiX ProofChain Platform:
1. [Network](#network)
2. [Decentralized IDentity (DID)](#decentralized-identity)
3. [Non-Linear Secret Sharing (NLSS)](#non-linear-secret-sharing)
4. [Gossip Protocol](#gossip-protocol)
5. [Pre-mined Tokens](#pre-mined-tokens)
6. [ProofChain](#proofchain)
7. [Transactions Ledger](#transactions-ledger)
8. [Peer-Peer Authentication](#peer-peer-authentication)
9. [Rubix Consensus Protocol](#rubix-consensus-protocol)
10. [Unique Ownership of Token](#unique-ownership-of-token)
11. [Double Spending Prevention](#double-spending-prevention)
12. [Fork Detection](#fork-detection)

## Network
Rubix Network contains nodes that join Rubix Private IPFS network. Private IPFS Network is deployed to ensure the data is securely stored within the nodes present in Rubix network. Swarm key plays a major role in setting up the private environment for nodes to communicate and store data. Rubix network generates a unique swarm key and must be present in the node which would like to be a part of the network.

Every node that joins our network discovers other nodes in the network with the peer discovery concept of IPFS. The communication between the nodes is peer-to-peer and is encrypted. Rubix uses LibP2P protocol in order to establish direct communication between the nodes. 

The data is known only to the nodes who are involved in the communication. There is no global broadcast of data across the network.

To know more about how LibP2P communication works, refer [LibP2P Protocol.](#https://docs.libp2p.io/tutorials/getting-started/go/)

## Decentralized IDentity
A Node joining the Rubix network acquires an identity called as Decentralized IDentity (DID). The Nodes are uniquely identified in the network using the DID. DID is self-generated by the node in the network by deriving from the Node's IPFS PeerID. In this way, IPFS PeerID and the DID of a particular node is mapped.

DID is a unique identifier which is designed to eliminate the dependency on the Central Authorities for providing the identity. This enables the controller of a DID to reveal the information he/she wishes to ensuring full privacy and security of DID.

### How a DID is generated?
To generate a DID, a node should have IPFS PeerID. The peerid is hashed using secure hashing algorithm SHA3-256 and is embedded into a gray scale image of size **1024 X 256**.

Once the DID for a node is generated, the DID is split using Non-Linear Secret Sharing ([NLSS](#non-linear-secret-sharing)) into 4 shares. The size of the shares obtained will be 64 times that of the DID image. 1 out of 4 shares is a Public Share and the remaining 3 are Private Shares. Public Share acts a unique Wallet Identifier like mobile number for communication. The Private shares should be securely stored by the node.

### How a DID is Verified?
The DID of a node is publicly verifiable. Anyone in the network can verify any node in the network. The DID and its Public Share is known to the whole network. To Verify the DID, the Private shares and Public Share is recombined using NLSS Schema which is used at the time of Split. If the recombined image matches the DID, then the DID is verified.

## Non-Linear Secret Sharing
Non-Linear secret sharing is a type of secret sharing scheme in which the shares are generated using Non-Linear function thus preventing Tompa-Wall attack. There are two types of schemas.
1. NLSS (m,n) - A total of **n** shares are generated for a secret out of which any **m** shares are required to recreate the secret.
2. NLSS (k,m,n) - Here, a secret is split into **n** shares and a total of **m** shares are required to regenerate the secret out of which **k** shares should be part of **m** shares. Any **m** shares without the presence of **k** shares will not recreate the secret.

The Shares obtained are of same size. Shares of different size will not recombine. If any of the share is corrupted or modified, the recombined image will not match the DID. So, the tampering of shares can be easily detected.

## Gossip Protocol
The Public Share generated from the DID should be known to the entire network for any node to communicate with other nodes. This is achieved by broadcasting the PeerID, DID and the Public Share of the nodes to the network using Gossip Protocol.

Every node in the network maintains an individual JSON Ledger of all data about other nodes they are aware of. If a node A discovers any other node B in the network, A broadcasts the data it has to B along with its own data. Now, B checks the data and adds only the record that is not present in its ledger and gives the data which A does not possess. In this way, all the nodes are synchronized.

For Example, Consider Node A has data of Node B, Node C and Node D has data of Node C, Node F. When A gossips the data it has, D checks the ledger whether it possess the data gossiped by A. D has C's data. So, D adds A's and B's data to its ledger. Since D knew that A does not have D, F's data, D gossips only that data to A. Now the ledger is Synchronized.

## Pre-mined Tokens

Transactions in Rubix network involves transfer of one or more tokens among the nodes. They are categorised into two types based on their design and application.

### Types of Tokens

**Asset Tokens** are Non-Fungible Tokens (NFT) that represents any real-world asset like Land, Shares etc., These are unique tokens and cannot be interchanged. They do not have a value unless bound to Utility tokens during a transaction. Hence, there is no restriction in the creation of Asset Tokens. 

**Utility Tokens** are limited in supply. They are pre-generated thereby eliminating the need of expensive miners. We generate 16.9 million unique tokens and each of these tokens are distributed among the Rubix Token Issuance nodes. They are publicly verifiable. 

## ProofChain
ProofChain is a chain of all proofs of transactions of a particular token. Every token in the network will have its own ProofChain. It represents the state of each token which is universally proven and publicly verifiable. Every proof represents the ownership of the token at that instance.

### ProofChain Formation
Whenever a node joins the network, node purchases tokens from Rubix token issuance nodes. The token is received along with the signature of token issuance node attached to the ProofChain of the token. This conveys that the token is received from token issuance node. Similarly whenever the token is transferred to another node, the signatures of the previous transactions and the current transaction is attached to it.

ProofChain contains the Token hash, Proof of the node holding the token, Public Share of the node, PeerID of the node. 

For example, If G (Token issuance node) transfers a token T to A, ProofChain P<sub>T</sub> contains P<sub>GT</sub> where P<sub>GT</sub> is the Proof of G. If A transfers to B, P<sub>T</sub> contains P<sub>GT</sub> + P<sub>AT</sub> . This way, the ProofChain is formed.

### ProofChain Verification
The signatures can be verified by using NLSS recombination of Proof and the corresponding positions of the Public Share of the node. The recombined result is matched with the positions of the DID of the respective node. If it matches the proof is verified. For detailed explanation, refer [P2P Authentication.](#peer-peer-authentication)

## Transactions Ledger
The data is not stored centrally anywhere in the network. The transaction details of a particular transaction are stored with the nodes who are involved in the transaction. Not every node in the network will have the same ledger. No node in the network will have the entire transaction ledger.

For example, Alice and Bob are transacting for the first time. The transaction ledger of both Alice and Bob will have only one entry in common i.e., the details of the transaction they are involved in. 

## Peer-Peer Authentication
Peer Authentication is important in a transaction. One should know whether the transaction is proposed by a genuine node or not before approving it. Since, DID of a node is split using NLSS, we use NLSS recombination to validate a node. 

The nodes involved in the transaction creates a Proof/Signature and sends to the nodes who wants to authenticate. 

### How is the Proof/Signature Created?
The Proof is created from the Private Shares of the node. Every node will have 3 Private Shares. 32 random positions are chosen from the data to be signed. The corresponding 32 position values are selected from the three Private Shares.

Since the private shares are images and each bit is NLSS split into 64 times that of original image, 32 * 64 = 2048 positions must be selected. For Example, if the random position is 40, then 63 positions next to 40 should be selected. From the three private shares, 2048 + 2048 + 2048 = 6144 bits are chosen as signature for that transaction.

### Proof Verification
Now that a node B gets the signature of other node A along with the data to be signed, the 32 random positions are recalculated from the data by node B. Since node B has node A's data broadcasted via Gossip protocol, 32 * 64 = 2048 positions are selected from the Public Share of node A and 32 positions are selected from DID of node A.

>NOTE :clipboard: Only 32 positions are selected from DID since NLSS recombines 64 bits into 1 bit.

The Signature and the Public Share positions are recombined using NLSS. The recombined result is matched with the DID positions of node A. If it matches, then the node is authenticated.

Once authenticated, the signature is added to the ProofChain.

## Rubix Consensus Protocol
Consensus is an agreement among few nodes in the network about a particular transaction. Nodes involved in consensus verify whether a transaction is genuine or not. Rubix Consensus protocol is a Light-weight Protocol which follows PBFT rule i.e., **2n +1** out of **3n + 1** nodes are required to reach an agreement.

### Quorum Selection
Quorum are the nodes running the consensus protocol. They are selected from the ProofChain of the Token that is being transferred in the transaction.

SHA3-256 Hash is calculated using the Public Share of the node holding the token, Token and the height of the ProofChain. A well-dispersed deterministic function is applied on the hash which gives 7 unique numbers. Those 7 people are selected from the ProofChain of the token for consensus. 

### Consensus
From PBFT rule out of 7 Quorum nodes, any 5 must approve for a transaction. Quorum approve based on the transaction data. 

Consider a transaction of token T involving Sender, Receiver and Quorum nodes. The sender node selects the Quorum from ProofChain of Token T and sends its signature, Receiver Public Share along with the transaction data which contains the SHA3-256 hash of Sender Public Share, Token and the ProofChain to be signed after verification to the Quorum. Only the Sender and receiver have full knowledge about the data. Quorum members only know the transaction data (hash) that they are supposed to sign.

The Quorum members check whether the transaction data to be signed is already signed anytime before. If it is signed, they do not agree for consensus. If not, they verify the sender using P2P Authentication schema mentioned in above section. Once, the sender is verified and the transaction is genuine, the Quorum sign on the transaction data along with the Receiver Public share and sends the proofs to the Sender. Any 5 out of 7 signatures are enough to reach consensus.

### Split and store
After the consensus is reached, the payload is formed using the PeerID's of the Sender and Receiver nodes along with the Token.

The payload obtained is a proof of consensus for a particular token. The payload is Split into 5 Shares using NLSS(1,3,5) schema. The Essential share is with the Sender and the Receiver whereas the other 4 shares are available with 4 out of 5 nodes who approved the transaction. All these shares with respect to a particular token are stored in IPFS.

### Quorum Signature Verification
Once 5 Signatures are obtained, the Sender sends the Quorum Signatures, Sender's Signature and the data to be signed i.e., SHA3-256 of Receiver Public Share, Token and the ProofChain to the receiver. The Receiver recalculates the hash used to select the Quorum and applies the deterministic function to select and verify whether the sender choose the genuine Quorum. Once the Quorum are selected by the receiver, signatures of Quorum are verified by using P2P Authentication schema by selecting corresponding positions of the Public Shares of the Quorum selected by the receiver. 

## Unique Ownership of Token
Once the Sender and the Quorum Signatures are verified by the Token's Ownership is verified. IPFS guarantees a single owner for a particular token at an instance. 

Suppose, the token T is being transferred from Alice to Bob. Alice uncommits the token from IPFS and sends the token to Bob. When Bob receives the token, he checks whether anyone else in the network is holding the token before committing. This is done by querying the network with the token's IPFS hash which gives the content only if someone else other than Alice holds it. So if nothing returns after querying, then no one else other than Bob has the token. So, Bob commits the token into IPFS.

This way unique ownership of a token is guaranteed.

## Double Spending Prevention
In one way it is avoided by the Quorum involved in the transaction. If any node tries to double spend the token, the same Quorum are selected since the selection is based on the Token. The Quorum owns a proof of every transaction they are involved in. The Quorum checks whether the transaction was signed earlier from the proofs they own. If it was signed by them, they don't agree for the same transaction again. In other way, it is avoided using IPFS which guarantees the [Unique Ownership](#unique-ownership-of-token) of the token at a particular instance.

For example, A transfer's a token to B. Now B is the new owner of the token. If A creates a digital copy of the same token and commit again to IPFS, there will be two committers of the same token A and B. Now when other node C wishes to acquire the token from the legitimate owner B, C finds out that A also committed the token. So, C will not proceed with the transaction unless A and B provide further proof. 

This way no node in the network can reuse the token that is already spent. Hence, preventing double spending.

## Fork Detection
Forking can be used for denial of service attacks. This is possible if nodes create some duplicate nodes in the network and transfer same token. 

Consider the following ProofChain P~T~ of token T.

P<sub>GT</sub> --> P<sub>AT</sub> --> P<sub>BT</sub> --> P<sub>CT</sub> --> P<sub>DT</sub> --> P<sub>ET</sub> 

This states that the initial owner of token T is node G and the current owner is node E. Token T has transferred from G to A, A to B, B to C, C to D and D to E. Now, when C creates a duplicate node C' and transfers the token to another node F. 

The ProofChain of node F looks like:

P<sub>GT</sub> --> P<sub>AT</sub> --> P<sub>BT</sub> --> P<sub>C'T</sub> --> P<sub>FT</sub>

When node F tries to commit the token, it can find out that two nodes C' and E committed the token. To detect the fork, F challenges C' and E to submit the ProofChains. By tracing the ProofChains backwards, it is found that node C made a fork.

To resolve a fork, each transaction goes through a consensus protocol and the Quorum sign on the transaction data. In case of a fork, the signature of the consensus Quorum is verified to decide the genuine chain.

