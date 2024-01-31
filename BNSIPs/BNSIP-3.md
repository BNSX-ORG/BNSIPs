# BNSIP-3:Tokenization of BNSx DID Ownership Proposal(Drafted)
## Proposal Overview

### Overview
This proposal aims to address the Converting to NFT of decentralized identity (DID) in the BNSx system. Tokenizing NFTs as representations of DID ownership is not only logical but also economically beneficial, providing more possibilities for DID within the economic model and enriching the ecosystem's value discovery function. NFT converted DID identities are more conducive to utilizing various infrastructure elements in the current ordinal ecosystem, making DID identity transactions more accessible. Simultaneously, DIDs indexed by BNSIP-2 consensus are considered authentic, so this proposal will also elaborate on how Indexers should identify fraud and confirm the authenticity of BNSx-DID-NFT.

### Proposal Details
#### Why Converting to NFT is Needed
Firstly, after successfully claiming a DID, users want an easy way to transfer ownership of a particular DID and to trade it conveniently. Therefore, we need to tokenize the ownership of DID. Secondly, when the community develops application SDKs related to DID in the future, a simple and convenient way is needed to look up DID owners on the blockchain. Finally, by verifying the identity signature of the NFT converted DID owner in the future, we can easily implement decentralized short-domain services, providing practical applications such as a WEB3.0 to WEB2.0 social gateway, personal email server, decentralized messaging system,decentralized Instagram-like photo album,private encrypted message board and more.
#### Converting to NFT Process
##### Preconditions:
Assuming Alice has completed the claiming process described in BNSIP-2, the off-chain indexer theoretically has confirmed that Alice's BTC address, for example, "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7," owns the DID "alice." For instance, Alice has pre-burned BNSx on the BTC network and used the BRC20-E-BNSIP2 protocol to send the following inscription to Satoshi Nakamoto's address:
```
//decoded JSON data
{ 
    "p": "brc-20",
    "op": "transfer",
    "tick": "BNSx",
    "amt": "100",
    "e":"BNSx-2",//extend:BNSX-2 protocol
    "d":{
      "op": "OP_COST_WITH_REG",
      "addr": "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7",
      "did": "alice"
    }
}
```
##### Converting:
Under the aforementioned conditions, Alice can at any time in the future, use the address "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7" on the BTC network to inscribe the following data as a representation of ownership for the "alice" DID which would be NFT converted.
```
{ 
     "p": "BNSx",
     "e":"BNSx-2",
     "d":{
         "op": "OP_REG",
         "addr": "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7",
         "did": "alice",
         "relatedTx":"4a15ce9cec0b4b34f08eae0d8484a02e344c36b99ce4aec2919290262afdc5fb"
     }
 }
```
#### Indexer Consensus Rules
1. The Indexer needs to verify the validity of the DID, as detailed in BNSIP-2.
2. Additionally, the Indexer needs to check the uniqueness of the DID-NFT. If the above inscription is repeatedly inscribed, only the first inscribed NFT is valid. Furthermore, it needs to check if the inscribed address and the address in the registration instruction correspond; the inscription must be initiated with the address in the "addr" field of the registration process to be considered a valid inscription. Finally, the Indexer only needs to check the address initiating the inscription; there are no requirements for the receiving address. In other words, if address A has permission for a certain DID, A can directly inscribe the NFT for that DID to A's address during the first inscription or to another address.
#### Marketplace:
Similar to other projects in Ordinals, there are already many Ordinals NFT trading marketplaces. Due to the need for a strict protocol to define the validity of DID-NFT, from an application perspective, we should not only provide registration of DID, making DID an NFT, and browsing of registered DID retrieval functions but also offer an open marketplace to users for trading DID-NFTs, eliminating the need to manually confirm whether the corresponding NFT is a valid DID-NFT on other marketplaces.
#### Constraints on DID Rules
1. Case-insensitive, for example, "Alice" and "alice" are the same DID.
2. Allowed character set: "**ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-.**"
## Development Roadmap
### Overview
Deliver an indexer implementation to realize this protocol and provide a CLI command-line program for Tokenization the ownership of an DID. Implement a user-friendly web interface for general user interaction.Develop a marketplace for users to trade DID-NFTs.

### Basic functionality
| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| 1. |  Opensource Indexer | Open-source implementation of this protocol's indexer |
| 2. |   CLI command program|  Command-line program capable of performing the designed functions|
| 3. |  Web UI | Web program for interactive use by ordinary users |
| 4. | Marketplace | An specfic marketplace for users to trade DID-NFTs  |
## Future Plans
- ntegration JS-SDK related to WEB3, provided to wallets, blockchain explorers, etc., for bidirectional lookup between DID and addresses.
- Short domain names services function definitions for our DID
- Control panel related to short domain names.
Above will be discussed in future proposals  
## Members
- Leader:Chef of BNSx Team

### Contact
- **Contact:** Email: support@bnsx.org

## Reference
- BNSIP-2 Proposal https://github.com/BNSX-ORG/BNSIPs/blob/main/BNSIPs/BNSIP-2.md
- BRC20-E Proposal https://github.com/BNSX-ORG/BNSIPs/blob/main/BNSIPs/BNSIP-1.md


