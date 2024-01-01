# BNSIP-2: BNSx Burn and Register Proposal(Drafted)
## Proposal Overview

### Overview
This proposal primarily describes how, within the BNSx system, we aim to achieve the fair, just, and transparent completion of the community roadmap. It emphasizes the functionality of registering DID NFTs only after completing the token burn. The proposal also outlines how the consensus for BTC off-chain indexing should verify registered domain NFTs, along with a scheme for determining the ownership transfer, transactions, and tracing of domain NFTs. The implementation of these functionalities will be based on the BRC20-E protocol, extending the BRC20 protocol.

### Proposal Details
#### Tokenomics
As described in our early roadmap, the process of registering NFT domains is tied to token burning and deflation. The number of burned tokens directly determines the length of the DID to be registered. Registration of domains with lengths of 2 characters and below is temporarily restricted. The indexing consensus will discard registration information for lengths less than or equal to 2. Short domains with 2 characters or fewer may be registered in the future through a special confirmation mechanism or auction after community empowerment.
- For registration of DIDs with a length of 7 or greater, one BNSx will be burned. For each reduction in the length of the registered DID, the required token quantity increases by a factor of 10. For example, if Bob wants to register "bob" as his DID, he needs to spend 10^(7-3) = 10,000 BNSx. If Alice wishes to register "alice" as her DID, she needs to spend 10^(7-5) = 100 BNSx.

  | DID Length | Example | BNSx Amount Required |Note|
  | -----: | ----------- | ------------- |------------- |
  | 1 | b | x |Registration not allowed temporarily |
  | 2 | cz | x |Registration not allowed temporarily  |
  | 3 | bob | 10000 | |
  | 4 | bnsx | 1000 | |
  | 5 | hello | 100 | |
  | 6 | bitmex | 10 | |
  | 7 | satoshi | 1 | One BNSx for lengths 7 and above|
  | 8 | coinbase | 1 | |


-  When the circulating supply exceeds 10.5 million, the burned tokens will be distributed among all staked BNSx mining HOLDERS once the burning reaches half of the total. The rules for the required BNSx token quantity will remain the same, but the tokens will no longer be removed from liquidity through burning. Instead, they will be distributed to all staked BNSx mining HOLDERS.
#### Burn and Register Design Specification
##### Burn Mechanism:
  The burn will use the BNSx-2 protocol extended from the BRC20-E protocol. BNSx will be sent to a special BTC network address. Addresses accepting BRC20-E(BNSx-2) transfers are categorized into two types: those recognized by the BTC community as early addresses used by Satoshi Nakamoto and those multi-signature addresses voted on by the community builders.
-  In the early stages, it is recommended to burn tokens directly to Satoshi's address. Since these tokens are unlikely to be operated again (unless Satoshi reappears), it can be reasonably assumed that this portion represents tokens removed from circulation.
-  The burn progress will be displayed. When the progress exceeds 80%, it is recommended to burn tokens using multi-signature addresses voted by the community. This address will distinguish the final burn quantity and provide initial staking rewards to HOLDERS. The remaining burn portion will also be sent to addresses previously used by Satoshi Nakamoto.
   | No. | Example |Note|
   | -----: | ----------- |------------- |
   | 1 | 12c6DSiU4Rq3P4ZxziKxzrL5LmMBrzjrJX  | Satoshi's address |
   | 2 | ......  | Community-voted multi-signature addresses |

##### Register Mechanism:
- BRC20-E's BNSIP2 Extension 
  Protobuf Message Design:
 ```
syntax = "proto3";
enum BNSxIP2xOP{
    OP_COST = 0;
    OP_REG  = 1;
    OP_COST_WITH_REG = 2;
}
message BNSxIP2{
    BNSxIP2xOP      op        = 1;
    optional string          addr      = 2;
    optional string did       = 3;
    optional string relatedTx = 4;
}
```
  explain:
| structure | item |explain|
| -----: | ----------- |------------- |
| BNSxIP2xOP | OP_COST  | Operation to spend tokens (indexer must check if the transfer address is correct) |
| BNSxIP2xOP | OP_REG  | Operation for individual registration (indexer needs to check if the relatedTx has a cost operation and if registration is incomplete)|
| BNSxIP2xOP | OP_COST_WITH_REG  | Simultaneously burn and register operation (saves fees, based on a first-comminted, first-get principle, may be preoccupied by others) |
| BNSxIP2 | op  | Details see the BNSxIP2xOP structure, operation type |
| BNSxIP2 | addr  | Related BTC address; used by the indexer to differentiate minted NFTs based on permissions when minting DID NFTs|
| BNSxIP2 | did  | In the reg-related operation, the DID to be registered|
| BNSxIP2 | relatedTx  | If someone else preoccupies the DID after the previous operation, to prevent wasting burned BNSx, allows re-registration of a new DID with the same conditions until successful|
##### Cases:
The examples below cover various possibilities, explaining the processing logic for indexers in each scenario.
For ease of demonstration, the information engraved in the ordinal system is actually based on BRC20-E's JSON data. For example:
```
{ 
    "p": "brc-20",
    "op": "transfer",
    "tick": "BNSx",
    "amt": "100",
    "e":"BNSx-2",
    "d":"CAISKmJjMXFrZ3Zza3h3ajdmMDNodWVmNnB3M2prbGNtdXdwcnYwc2E1eWZ3NxoFYWxpY2U="
}
```
For clarity, we convert the protobuf field "d" into a JSON sub-node. For instance, the above "d" field, after base64 decoding and protobuf parsing, results in:
```
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
Workflow:
1. Burn and register by engraving the following inscription:
   ```
    { 
        "p": "brc-20",
        "op": "transfer",
        "tick": "BNSx",
        "amt": "100",
        "e":"BNSx-2",
        "d":{
            "op": "OP_COST_WITH_REG",
            "addr": "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7",
            "did": "alice"
        }
    }
   ```
   Transfer the above inscription to the specified address in the Burn Mechanism through the normal BRC20 process. If the first DID in the transfer is Alice and is sent to the address in the Burn Mechanism collection, it indicates a successful registration of the DID "alice."
2. Suppose the transfer was successful, but when the data in TXID 1 was submitted to the chain, someone else had already registered the DID "alice" in advance. In this case, the operator spent 1,000 BNSx but did not register the desired DID. They are then allowed to engrave an operation record like the following to attempt registering another DID until successful:
   ```
   { 
        "p": "BNSx",
        "e":"BNSx-2",
        "d":{
            "op": "OP_REG",
            "addr": "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7",
            "did": "alexs",
            "relatedTx":"86b0ef5978f004370cd0017724912535b880b8a2fec49b1a2f3ee146669ae36d"
        }
    }
   ```
Suppose when the above inscription is engraved onto the BTC network, the previous DID "alexs" has not been preoccupied. In that case, DID "alexs" is marked as owned by "bc1qkgvskxwj7f03huef6pw3jklcmuwprv0sa5yfw7." In this process, the indexer needs to check if the initiating address of this inscription and the sending address of TX "86b0ef5978f004370cd0017724912535b880b8a2fec49b1a2f3ee146669ae36d" are the same. If not, it can be ignored.
3. Users or agents can save gas fees and provide a quick registration service for others by spending tokens in advance or spending tokens when gas fees are low. To do this, they initiate a cost inscription first:
  ```
  { 
        "p": "brc-20",
        "op": "transfer",
        "tick": "BNSx",
        "amt": "100",
        "e":"BNSx-2",
        "d":{
            "op": "OP_COST"
        }
    }
  ```
Then send it to the Burn Mechanism collection's address. Assume the TXID is "4a15ce9cec0b4b34f08eae0d8484a02e344c36b99ce4aec2919290262afdc5fb." 
Through PSBT programming or encoding the user's address into the inscription data, after the above transaction is fully committed to the BTC network, the agent can provide a fast registration service for others and earn registration fees. This is achieved by engraving the following inscription with the same address:
```
{ 
        "p": "BNSx",
        "e":"BNSx-2",
        "d":{
            "op": "OP_REG",
            "addr": "bc1q04t6kw9gmkl9sxlc4yjxl03srzqc9lw2w8fyq0",
            "did": "hello",
            "relatedTx":"4a15ce9cec0b4b34f08eae0d8484a02e344c36b99ce4aec2919290262afdc5fb"
        }
    }
```
Once this inscription is engraved (subject to FCFG principle), it grants ownership of the DID "hello" to the address "bc1q04t6kw9gmkl9sxlc4yjxl03srzqc9lw2w8fyq0."

## Development Roadmap

### Overview
Deliver an indexer implementation to realize this protocol and provide a CLI command-line program for spending BNSx and registering DIDs. Implement a user-friendly web interface for general user interaction.

### Basic functionality

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| 1. |  Opensource Indexer | Open-source implementation of this protocol's indexer |
| 2. |   CLI command program|  Command-line program capable of performing the designed functions|
| 3. |  Web UI | Web program for interactive use by ordinary users |
## Future Plans
- How to bind the ownership of DID to an Ordinal NFT
- How Indexer check the relationship between the specific Ordinal NFT and DID's ownership
Above will be discussed in future proposals  
## Members
- Leader:Chef of BNSx Team

### Contact
- **Contact:** Email: support@bnsx.org

## Reference
- BRC20 https://domo-2.gitbook.io/brc-20-experiment/
- Protobuf https://protobuf.dev/  
- BRC20-E Proposal https://github.com/BNSX-ORG/BNSIPs/blob/main/BNSIPs/BNSIP-1.md


