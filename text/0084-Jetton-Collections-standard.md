- **TEP**: 
- **title**: Jetton Collections Standard
- **status**: Draft
- **type**: Contract Interface
- **authors**: [Ivan Klimov](https://github.com/ivklim-ton-play) 
- **created**: 03.09.2022

# Summary

A standard interface for Jetton collections. 

# Motivation
The idea is simple and seeks to create a standard that can allow you to combine Jettons in collection and combine Jetton collections into a collection of Jetton Colletion.

- The way of ownership changing.
- The way of association of Jettons into collections.

# Guide

This standard is needed when you need to have several different Jettons, but at the same time be able to confirm with one step that they all belong to the same collection on the blockchain.

For example, you have access cards for entering the site and with different categories of access. Knowing the actual address of the collection, you can easily check the identity of Jetton.

For example, a similar situation for the game. In the game, there are various items released as Jetton and can be combined into one collection (100 iron armor, 200 wooden armor, and so on)

## Jetton collection Metadata
Each Jetton collection itself has its own metadata ([TEP-64](https://github.com/ton-blockchain/TEPs/blob/master/text/0064-token-data-standard.md)). It contains some info, such as title and associated image. Metadata can be stored offchain (smart contract will contain only a link to json) or onchain (all data will be stored in smart contract).

### Jetton collection metadata example (offchain):
```json
{
   "image": "https://image.com/img.png",
   "name": "Huebel Bolt collection",
   "description": "Official collection of the Huebel Company"
}
```

It uses the [NFT collection metadata](https://github.com/ton-blockchain/TEPs/blob/master/text/0064-token-data-standard.md#nft-metadata-attributes).

Offchain metadata is published for example on web 

## Useful links
1. [Reference Jetton collection of Jettons implementation](https://github.com/ivklim-ton-play/ton-SFT/tree/feature/jetton-collection-contract)

# Specification

Here and following we use:
 - "Jetton master" is used to mint new jettons from [Jettons](https://github.com/ton-blockchain/TEPs/blob/master/text/0074-jettons-standard.md) standart. But for Jetton collection it contains some additional methods for the collection. 
 - "Jetton collection" - collection for Jetton masters or Jetton collections like items. Each item has its own unique id. Based on the idea of [nft-collection](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md). Smart-contracts called **"[jetton-collection](https://github.com/ivklim-ton-play/TEPs/edit/Update-to-Jetton-Collections/text/0084-Jetton-Collections-standard.md#jetton-collection-smart-contract)"**.

### Example: 

#### Jetton collection for Jettons
You release a jetton-collection with circulating supply of 200 Jettons for id = 0, and circulating supply of 100 Jettons for id = 1.
- We have 2 owners.
  - Owner_1 has 100 Jettons by id = 0 and 100 by id = 1.
  - Owner_2 has 100 Jettons by id = 0.

We need to deploy 6 contracts: 
- **1** jetton-collection smart-contract.
- **1** jetton-master smart-contract by id = 0 and **1** jetton-master smart-contract by id = 1.
- For owner_1 we need **1** jetton-wallet smart-contract from jetton-master by id = 0 and **1** jetton-wallet smart-contract from jetton-master by id = 1.
- For owner_2 we need **1** jetton-wallet smart-contract from jetton-master by id = 0.

#### Jetton collection for Jetton collections
Here we need a general collection contract for several collections. For example, let's call it the Super Jetton collection.

Then we will add only one general contract. And contracts for each Jetton collection (described above).

## Extended Jetton master smart contract

Must implement:

### Internal message handlers

#### 1. [`get_static_data` as in NFT item smart contract](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#2-get_static_data)

### Get-methods

1. `get_jetton_collection_data()` returns `(int init?, int index, slice collection_address)` 

   `init?` - if not zero, then this Jetton master is fully initialized and ready for interaction

   `index` - (integer) - index in Jetton collection
 
   `collection_address` - (MsgAddress) - address of the smart contract of the collection to which this Jetton master belongs. 
 
## Jetton collection smart contract
 
It is assumed that the smart contract of the collection deploys smart contracts of Jettons masters of this collection.

Must implement:

### Get-methods
1. [**`get_collection_data()`** as in NFT Collection smart contract](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#get-methods-1)

2. [**`get_nft_address_by_index(int index)`** as in NFT Collection smart contract](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#get-methods-1)

3. [**`get_nft_content(int index, cell individual_content)`** as in NFT Collection smart contract](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#get-methods-1)

# Drawbacks
There is no way to get current owner of Jetton collection and Jetton master onchain because TON is an asynchronous blockchain. When the message with info about Jetton collection owner will be delivered, this info may become irrelevant, so we can't guarantee that current owner hasn't changed.

There is no way to get actual wallet balance onchain, because when the message with balance will arrive, wallet balance may be not actual.

# Rationale 
Look in [NFT Rationale and alternatives](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#rationale-and-alternatives)

# Prior art
1. [Ethereum NFT Standard (EIP-1155)](https://eips.ethereum.org/EIPS/eip-1155)

# Unresolved questions

# Future possibilities

# TL-B schema

1. [TL-B schema from NFT](https://github.com/ton-blockchain/TEPs/blob/master/text/0062-nft-standard.md#tl-b-schema)  
`transfer` from NFT is not required in the Jetton Collections standard

# Changelog
[16 Sep 2022] The standard has been redesigned for greater compatibility with Jettons  
[21 Sep 2022] Use "sft":"true" in metadata instead of "sft" : {}  
[23 Sep 2022] For SFT wallet use get_wallet_data()  
[05 Oct 2022] Removed the transfer requirement for SFT minter
[23 Oct 2022] The standard has been redesigned as extended for Jetton like Jetton Collections
