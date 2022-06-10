# consensus-workshop-2022

## What is ethers.js

ethers.js is a monorepo of libraries for developing applications for Ethereum for any Ethereum-compatible blockchain.

Building with ethers.js does not always mean installing the ethers project and importing the entire module. The library is broken up into many smaller libraries which can be imported individually if designing libraries for Ethereum or web applications and attempting to manage bloat.


## ethers.js subpackage breakdown

The following modules can be installed and imported into a program or application independently:

 - @ethersproject/abi
    - Tools for encoding/decoding ABI i.e. the function signatures and calldata sent with a transaction (the inputs of a function call to a Solidity contract)
 - @ethersproject/abstract-provider
    - Base provider classes that are inherited by all ethers.js providers such as InfuraProvider, JsonRpcProvider, etc
 - @ethersproject/abstract-signer
    - The base classes for ethers.Signer instantiations, includes VoidSigner which can be extended to create custom Signers
 - @ethersproject/address
    - Includes functions for checksumming addresses, computing contract addresses ahead of time, etc
 - @ethersproject/asm
    - Library for assembling/disassembling EVM bytecode from raw opcodes
 - @ethersproject/base64
    - Libraries for encoding base64
 - @ethersproject/basex
    - Libraries for encoding values into an arbitrary base (other than base64, maybe base59 etc)
 - @ethersproject/bignumber
   - Base class for BigNumber, which is the number type used in ethers.js or returned from Contracts / other structures that have large numbers (JS can only encode a 53-significant bit value in a Number)
 - @ethersproject/bytes
    - Contains functions arrayify and hexlify, used for encoding any type to a Uint8Array or a hex string, respectively
 - @ethersproject/cli
    - Lesser known library that is actually a CLI tool called `ethers` which allows you to evaluate ethers.js expressions in shell scripts, send transactions, or check balances. See an extension of it here [https://github.com/raymondpulver/ethers-cli-mod](https://github.com/raymondpulver/ethers-cli-mod)
 - @ethersproject/constants
   - Contains useful constants such as MaxUint256 (256 bit value with all bits set to 1, equal to 1 << 256) and AddressZero (the address 0x0000000000000000000000000000000000000000)
 - @ethersproject/contracts
    - Base class for ethers.Contract, which is the usual way we interact with Ethereum systems
 - @ethersproject/experimental
    - Some oddities in ethers.js
 - @ethersproject/hardware-wallets
    - Library to interface with Ledger
 - @ethersproject/hash
    - Libraries to hash a personal message with EIP-191
 - @ethersproject/hdnode
    - Libraries for BIP32 and BIP39 key derivation from a master seed or master public seed
 - @ethersproject/json-wallets
    - Libraries to encrypt private keys with pbkdf2 + a password
 - @ethersproject/keccak256
    - Libraries to use keccak256
 - @ethersproject/logger
    - Built-in logger in ethers.js
 - @ethersproject/networks
    - Contains network definitions via `getDefaultProvider` for all the following networks
      - homestead
      - mainnet
      - morden
      - ropsten
      - testnet
      - rinkeby
      - kovan
      - goerli
      - kintsugi
      - classic
      - classicMorden
      - classicMordor
      - classicTestnet
      - classicKotti
      - xdai
      - matic
      - maticmum
      - optimism
      - optimism-kovan
      - optimism-goerli
      - arbitrum
      - arbitrum-rinkeby
      - bnb   
      - bnbt
 - @ethersproject/pbkdf2
    - Key derivation for ethers.js, can be used with `ethers.Wallet#encrypt`
 - @ethersproject/properties
    - Functios for defineReadOnly, used all over ethers.js to make immutable objects 
 - @ethersproject/providers
    - JsonRpcProvider
      - Usually you will use this provider to connect to an RPC URL. It can be used to connect to networks not supported by something like InfuraProvider
    - InfuraProvider
      - A special provider that is just like JsonRpcProvider, but it has preset RPC URLs for Infura and allows you to construct with your API key. The major difference is, when fetching events with this provider vs JsonRpcProvider, it will use the WebSocket transport, since Infura does not provide an events API over HTTP alone
    - WebSocket provider
      - Connect to a ws or wss RPC
    - BaseProvider
      - Derive your own Provider classes to implement special behavior
 - @ethersproject/random
    - RNG
 - @ethersproject/rlp
    - RLP encoding, used all over Ethereum to encode lists or structures as bytes
 - @ethersproject/sha2
    - SHA2 hash algorithm
 - @ethersproject/shims
    - Browser compatibility
 - @ethersproject/signing-key
    - Class to perform elliptic curve operations when using an ethers.Wallet
 - @ethersproject/solidity
    - Functions to pack structures for use with Solidity `abi.encodePacked` or `keccak256(abi.encodePacked(...d))`
 - @ethersproject/strings
    - UTF-8 handling functions to encode/decode Solidity strings
 - @ethersproject/testcases
    - ethers.js test setup
 - @ethersproject/tests
    - ethers.js tests
 - @ethersproject/transactions
    - Functions to serialize/parse Transaction structures as well as recover the original signer from a signed transaction structure
 - @ethersproject/units
    - Functions to parse/format large number values, specifically for parsing a wei quantity to ether, or formatting token balances as a human readable number (raw balances are stored as very large numbers corresponding to the amount of decimals an ERC20 uses)
 - @ethersproject/wallet
    - A special ethers.Signer which can accept a private key or mnemonic, to sign transactions or messages entirely within the program, instead of using something like a MetaMask signer
 - @ethersproject/web
    - XHR related operations for use with in-browser fetching against an RPC
 - @ethersproject/wordlists
    - Wordlist base class used by the BIP32 / BIP39 implementations in other ethers.js packages
 - @ethersproject/ethers
    - Package used as the main export of ethers.js if the entire monorepo is imported via `import { ethers } from 'ethers';` or `require('ethers')` 
    - Simply exists to bundle all of the libraries into one module

## Extending ethers.js for applications

There is a great deal of extensibility within @ethersproject/providers and @ethersproject/abstract-signer

When a signer is used, in an @ethersproject/contracts Contract object or standalone, it will call the functions of the provider instance available on the signer object as `signer.provider` and it will call its own internal Signer API to fetch all the required data to sign the message.

Refer to @ethersproject/transactions to see the structure of an Ethereum transaction. Legacy transactions are supported by ethers.js as well as EIP-2930 and EIP-1559 extensions, depending on the target nework (constructing a TRON transaction requires overriding a Signer to use legcy transactions, for example)

The fields of a transaction object are

{ chainId, nonce, maxPriorityFeePerGas, maxFeePerGas, gasLimit, to, value, data, v, r, s }

Where v, r, s is the signature of the transaction, for a transaction that has been signed.

When a signer constructs a transaction object, before it actually signs it, it will fetch values for these fields using the provider that it is connected to.

chainId: `(await signer.provider.getNetwork()).chainId`
nonce: `await signer.provider.getTransactionCount(await signer.getAddress())`
gasPrice: `await signer.provider.getGasPrice()`
gasLimit: `await signer.provider.estimateGas({
  to: transaction.to,
  data: transaction.data,
  from: await signer.getAddress(),
  value: transaction.value
})`
to: transaction.to,
value: transaction.value


If we have a Signer object, we can reassign functions listed here to intercept behavior

## Implementing a Signer

To create a custom Signer, refer to the implementation of @ethersproject/abstract-signer and review the VoidSigner class. You can extend the VoidSigner class as long as you implement functions below:

```js

import { VoidSigner } from '@ethersproject/abstract-signer';
class MySigner extends VoidSigner {
  constructor({
    provider,
    address
  }) {
    super(address, provider);
    // other logic
  }
  async signMessage(msg) {
    // return hex string of signature
  }
  async sendTransaction(tx) {
    // all logic to dispatch transaction
  }
}
```


## Examples

[https://github.com/freelogstudio/ethers-gasnow](https://github.com/freelogstudio/ethers-gasnow)

Construct a `getGasPrice` function that will fetch using the gasnow metric, to avoid transactions getting stuck for low gasPrice reported by the RPC you're using


[https://github.com/kungfuflex/ethers-polygongastracker](https://github.com/kungfuflex/ethers-polygongastracker)

Similar logic as ethers-gasnow, but meant for Polygon RPC providers


[https://github.com/freelogstudio/ethers-flashbots](https://github.com/freelogstudio/ethers-flashbots)

Signer object that wraps an existing signer which supports the eth_sendPrivateTransaction RPC call supported by flashbots. Can be used with the Alchemy API free tier.

## Other Possibilities

- Create a smart contract wallet factory in Solidity then  define a Signer class that automatically encodes and sends transactions through the smart contract proxy.

- Create a signer that can handle multichain -- Expose a function to change provider and support all possible networks within the same web application, without duplicating logic to handle different chains


## Final Notes

In reverse engineering, we often do not get to look at a server's source code to understand how to interact with it. We have to de-obfuscate and analyze a client. For learning to build with Ethereum, you can think of the ethers.js packages as the client, to understand better how to work with Ethereum systems.

There is no documentation for the internals of ethers.js. If you are writing libraries for ethers.js, or really even just using the ethers.js library to build an application, you have to search and read through the ethers.js source. The repo can be cloned from

https://github.com/ethers-io/ethers.js

Every serious ethdev should clone and skim through this codebase at least once.

Good luck!

- flex
