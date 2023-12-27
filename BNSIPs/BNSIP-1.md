# BNSIP-1: BRC20-E Proposal(BRC20 Extension Proposal)
## Proposal Overview

### Overview

The BRC20 protocol is a technology for issuing fungible tokens on the BTC chain based on the Ordinal sequence theory. It is implemented using specific JSON structures to provide a fair distribution solution for tokens on the BTC chain. However, the functionality of the BRC20 protocol is limited. We propose a protocol extension called BRC20-E to seamlessly integrate the BRC20 infrastructure and extend its own ecosystem, protocols, and functionalities, allowing other projects to utilize BRC20 seamlessly.
The BRC20-E protocol extends the original BRC20 functionality by adding the following fields to the JSON structure:
```
{
    "e":"BNSX-1",//extend:BNSX-1 protocol
    "d":"Cgh3ZWRlZGV3dxIDAQIDGAAiBQECAwQF"//data:base64 encoded data
}
```
- A complete BRC20-E protocol JSON example is as follows:
```
{ 
    "p": "brc-20",
    "op": "transfer",
    "tick": "ordi",
    "amt": "100",
    "e":"BNSX-1",//extend:BNSX-1 protocol
    "d":"Cgh3ZWRlZGV3dxIDAQIDGAAiBQECAwQF"//data:base64 encoded data
}
```

### Proposal Details
#### Motivation
Given the widespread infrastructure support for BRC20, including wallets and indexers, we believe that adding extension features without disrupting the original BRC-20 protocol's semantics and functionality is the best practice for advancing the BTC-L1 ecosystem. Our proposed design aims to provide a solution from the perspectives of protocol uniformity, protocol continuity, protocol immutability, and minimal data submission to BTC-L1 for cost efficiency, saving users transaction fees.
Goals:
- Uniformity of extending the protocol: All indexers should be able to decode BRC20-E JSON directly from the chain and understand its meaning.
- Continuity of extending the protocol: Allow third parties to deploy and propose their own extensions to enrich the BTC-L1 ecosystem or upgrade their protocols.
- Immutability of extending the protocol: Once a consensus is reached on the extension protocol, it is deployed on the BTC network, and the extension protocol is permanently stored and immutable.
- Minimize data addition to the original protocol: The extension protocol aims to add minimal data to the original protocol to minimize expenses for the BTC network, maximizing user gas fee savings.

#### BRC20-E Protocol Design Specification
##### About the "e" field:
  The "e" field uses a globally unique name to mark the structual messages of the extension protocol. It follows the first-come-first-served principle. When a third party needs to extend the BRC20 protocol, anyone can register their extension protocol design by inscribing the following content:
```
//Register extension protocol bnsx-1
{
    "p":"brc-20-e",//extend brc20
    "op":"reg",//operation:register
    “doc”:"http://docs.bnsx.org",//Protocol detailed introduction document
    "n":"bnsx-1",//name:bnsx-1
    "d":"c3ludGF4ID0gInByb3RvMyI7Cm1lc3NhZ2UgZXhhbXBsZU1lc3NhZ2UKewogIHN0cmluZyBleGFtcGxlU3RyaW5nID0gMTsKICBieXRlcyBleGFtcGxlQnl0ZXMgPSAyOwogIHVpbnQzMiBleGFtcGxlSW50ID0gMzsKICByZXBlYXRlZCB1aW50MzIgcmVwZWF0ZWRJbnQgPSA0Owp9Cg=="//data:base64 encoded data
}
//Register multiple extension protocols at once
{
    "p":"brc-20-e",//extend brc20
    "op":"reg",//operation:register
    "l":[
        "bnsx-2":"c3ludGF4ID0gInByb3RvMyI7Cm1lc3NhZ2UgZXhhbXBsZU1lc3NhZ2UKewogIHN0cmluZyBleGFtcGxlU3RyaW5nID0gMTsKICBieXRlcyBleGFtcGxlQnl0ZXMgPSAyOwogIHVpbnQzMiBleGFtcGxlSW50ID0gMzsKICByZXBlYXRlZCB1aW50MzIgcmVwZWF0ZWRJbnQgPSA0Owp9Cg==",//data:base64 encoded data
        "bnsx-3":"c3ludGF4ID0gInByb3RvMyI7Cm1lc3NhZ2UgZXhhbXBsZU1lc3NhZ2UKewogIHN0cmluZyBleGFtcGxlU3RyaW5nID0gMTsKICBieXRlcyBleGFtcGxlQnl0ZXMgPSAyOwogIHVpbnQzMiBleGFtcGxlSW50ID0gMzsKICByZXBlYXRlZCB1aW50MzIgcmVwZWF0ZWRJbnQgPSA0Owp9Cg=="//data:base64 encoded data
    ]
}
```
##### About the "d" field:
-  The "d" field stores data that is BASE64-encoded. In the inscription of the extension protocol, this field should be decoded through BASE64 and then decoded with the protocol's protobuf design. For example, the bnsx-1 extension protocol's decoding results in the following semantic information:
 ```
c3ludGF4ID0gInByb3RvMyI7Cm1lc3NhZ2UgZXhhbXBsZU1lc3NhZ2UKewogIHN0cmluZyBleGFtcGxlU3RyaW5nID0gMTsKICBieXRlcyBleGFtcGxlQnl0ZXMgPSAyOwogIHVpbnQzMiBleGFtcGxlSW50ID0gMzsKICByZXBlYXRlZCB1aW50MzIgcmVwZWF0ZWRJbnQgPSA0Owp9Cg==//the original data
```
after base64 decoding:
```
syntax = "proto3";
message exampleMessage
{
  string exampleString = 1;
  bytes exampleBytes = 2;
  uint32 exampleInt = 3;
  repeated uint32 repeatedInt = 4;
}
 ```
- The "d" field stores data that is BASE64-encoded. In general usage of extension the BRC20 protocol，this field needs to be first BASE64-decoded and then decoded using the Protobuf design of the extension protocol. For example, decoding the extension protocol "bnsx-1" after BASE64 decoding results in the following original semantic information:
```
"d":"Cgh3ZWRlZGV3dxIDAQIDGAAiBQECAwQF"
```
After BASE64 decoding and Protobuf decoding, you get the original semantic information:
```
{
  "repeatedInt": [
    1,
    2,
    3,
    4,
    5
  ],
  "exampleString": "wededeww",
  "exampleBytes": [
    1,
    2,
    3
  ],
  "exampleInt": 0
}
```


## Development Roadmap

### Overview
Delivery of a BRC20-E protocol registry center to assist users in submitting new extension protocol upgrades and viewing existing protocols.And also providing the opensource SDK for "indexers" and front-end js Lib

### Basic functionality

| Number | Deliverable | Specification |
| -----: | ----------- | ------------- |
| 1. | Extension protocol registry center | The registry center will allow any organization or individual to inscribe their extension protocol upgrade information on the BTC chain. |
| 2. | Extension protocol browsing interface | The browsing interface will allow anyone to view the designs of extension protocols attached to BRC20 by other organizations or individuals.|
| 3. | Extension protocol indexing SDK | An indexing SDK provided to developers for use. |
| 4. | Extension protocol JS Lib | An converting JS Lib for developers |


## Future Plans (optional)

- BRC20-E currently focuses on extending the parsing functionality of off-chain indexers. It provides a strong extension design for users and the community to guide indexers in achieving more powerful functionalities. Whether further extension into off-chain VM consensus is needed is a topic for discussion in the future. New BNSIps would dress this issue.

## Members
- Leader:Chef of BNSx Team

### Contact
- **Contact:** Email: support@bnsx.org

## Reference

- BRC20-S Proposal https://brc20-s.gitbook.io/bitcoin-ecosystem/
- BRC20 https://domo-2.gitbook.io/brc-20-experiment/
- Protobuf https://protobuf.dev/  



