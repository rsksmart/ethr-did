<p align="middle">
    <img src="https://www.rifos.org/assets/img/logo.svg" alt="logo" height="100" >
</p>
<h3 align="middle"><code>ethr-did</code></h3>
<p align="middle">
    Ethr DID + RSK support
</p>
<p align="middle">
    <a href="https://badge.fury.io/js/%40rsksmart%ethr-did">
        <img src="https://badge.fury.io/js/%40rsksmart%2Fethr-did.svg" alt="npm" />
    </a>
</p>

```
npm i @rsksmart/ethr-did
```

### Disclaimer

This package is a fork of [ethr-did](https://github.com/uport-project/ethr-did) used to pre-release RSK-oriented features. The features on this package will be PRed into `ethr-did`.

**Versioning** This package will use the next sem-version minor yet not released to npm, post-fixed with beta versioning. E.g.: `v1.1.0-beta.1`

**Collaborate** Point your PR to `develop`. `rsksmart-org` branch will be rebased on `develop` once the PR is approved. Other PR pointing to `rsksmart-org` will be responsible for updating readme + updating version after feature is rebased. `develop` will be rebased onto `upstream/develop` when `ethr-did` package is updated.

**PRs included**

- [Add rsk support](https://github.com/uport-project/ethr-did/pull/50)

# Ethr-DID Library

[DID Specification](https://w3c-ccg.github.io/did-spec/) | [ERC-1056](https://github.com/ethereum/EIPs/issues/1056) | [Getting Started](/docs/guides/index.md)

[FAQ and helpdesk support](http://bit.ly/uPort_helpdesk)

This library conforms to [ERC-1056](https://github.com/ethereum/EIPs/issues/1056) and is intended to use Ethereum addresses as fully self-managed [Decentralized Identifiers](https://w3c-ccg.github.io/did-spec/#decentralized-identifiers-dids) (DIDs), it allows you to easily create and manage keys for these identities.  It also lets you sign standards compliant [JSON Web Tokens (JWT)](https://jwt.io) that can be consumed using the [DID-JWT](https://github.com/uport-project/did-jwt) library.

This library can be used to create a new ethr-did identity.  It allows ethr-did identities to be represented as an object that can perform actions such as updating its did-document, signing messages, and verifying messages from other dids.

Use this if you are looking for the easiest way to start using ethr-did identities, and want high-level abstractions to access its entire range of capabilities.  It encapsulates all the functionality of [ethr-did-resolver](https://github.com/decentralized-identity/ethr-did-resolver) and [ethr-did-registry](https://github.com/uport-project/ethr-did-registry).

A DID is an Identifier that allows you to lookup a DID document that can be used to authenticate you and messages created by you.

Ethr-DID provides a scalable identity method for Ethereum addresses that gives any Ethereum address the ability to collect on-chain and off-chain data. Because Ethr-DID allows any Ethereum key pair to become an identity, it is more scalable and privacy-preserving than smart contract based identity methods, like our previous [Proxy Contract](https://github.com/uport-project/uport-identity/blob/develop/docs/reference/proxy.md).

This particular DID method relies on the [Ethr-Did-Registry](https://github.com/uport-project/ethr-did-registry). The Ethr-DID-Registry is a smart contract that facilitates public key resolution for off-chain (and on-chain) authentication. It also facilitates key rotation, delegate assignment and revocation to allow 3rd party signers on a key's behalf, as well as setting and revoking off-chain attribute data. These interactions and events are used in aggregate to form a DID's DID document using the [Ethr-Did-Resolver](https://github.com/uport-project/ethr-did-resolver).

An example of a DID document resolved using the Ethr-Did-Resolver:

```
{
  '@context': 'https://w3id.org/did/v1',
  id: 'did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a',
  publicKey: [{
       id: 'did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a#owner',
       type: 'Secp256k1VerificationKey2018',
       owner: 'did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a',
       ethereumAddress: '0xb9c5714089478a327f09197987f16f9e5d936e8a'}],
  authentication: [{
       type: 'Secp256k1SignatureAuthentication2018',
       publicKey: 'did:ethr:0xb9c5714089478a327f09197987f16f9e5d936e8a#owner'}]
}
```

On-chain refers to something that is resolved with a transaction on a blockchain, while off-chain can refer to anything from temporary payment channels to IPFS.

It supports the proposed [Decentralized Identifiers](https://w3c-ccg.github.io/did-spec/) spec from the [W3C Credentials Community Group](https://w3c-ccg.github.io).


## DID Method

A "DID method" is a specific implementation of a DID scheme that is identified by a `method name`. In this case, the method name is `ethr`, and the method identifier is an Ethereum address.

To encode a DID for an Ethereum address, simply prepend `did:ethr:`


For example:

`did:ethr:0xf3beac30c498d9e26865f34fcaa57dbb935b0d74`

## Configuration

```js
import EthrDID from 'ethr-did'

// Assume web3 object is configured either manually or injected using metamask


const ethrDid = new EthrDID({address: '0x...', privateKey: '...', provider})
```

| key | description| required |
|-----|------------|----------|
|`address`|Ethereum address representing Identity| yes |
|`registry`| registry address (defaults to `0xdca7ef03e98e0dc2b855be647c39abe984fcf21b`) | no |
|`provider`| web3 provider | no |
|`web3`| preconfigured web3 object | no |
|`rpcUrl`| JSON-RPC endpoint url | no |
|`signer`| [Signing function](https://github.com/uport-project/did-jwt#signer-functions)| either `signer` or `privateKey` |
|`privateKey`| Hex encoded private key | yes* |

**Note**
An instance created using only an address can only be used to encapsulate an external ethr-did (one where there is no access to the private key).
This instance will not have the ability to sign anything, but it can be used for a subset of actions:

*  provide its own address (`ethrDid.address`)
*  provide the full DID string (`ethrDid.did`)
*  lookup its owner `await ethrDid.lookupOwner()`
*  verify a JWT `await ethrDid.verifyJwt(jwt)`
