KOMSCO Trusted Platform DID Method Specification
=================
18th April 2020

# Table of Contents
1. [Abstract](#abstract)
2. [DID Method Name](#name)
3. [Method Specific Identifier](#identifier)
    1. [Example](#example1)
4. [DID Document](#document)
    1. [Example](#example2)
5. [JSON-LD Context Definition](#ld)
6. [CRUD Operation Definitions](#crud)
    1. [Create (Register)](#create)
    2. [Read (Resolve)](#read)
    3. [Update](#update)
    4. [Delete](#delete)
7. [Security Considerations](#security)
8. [Privacy Considerations](#privacy)
9. [References](#references)
 
# Abstract <a name="abstract"></a>
KOMSCO, as a government-designated national ID card/passports manufacturer and issuer in the Republic of Korea, has always strived hard to respond to the world's trends and to achieve protection against forgery and alteration for a long time.
KOMSCO Trusted platform is the blockchain based identity system. KOMSCO Trusted Platform provides services in the public sector such as safe payment and ID authentication as well as information protection and management services. 
KOMSCO Decentralized Identifiers is a distributed identifier designed to provide a way for a community connected to the KOMSCO Trusted platform to uniquely identify an individual, organization, or IoT device. The role of a KOMSCO DID is to provide a service that supports id-authentication and personal information verification. 

The KOMSCO DID method specification conforms to the requirements specified in the Decentralized Identifiers (DIDs) v1.0 [**[1]**](https://w3c.github.io/did-core/), currently published by the W3C Credentials Community Group. 
For more information about DIDs and DID method specifications, please see the DID Spec and the DID Primer.
 
# DID Method Name <a name="name"></a>

The namestring that shall identify this DID method is: `komsco`

A DID that uses this method MUST begin with the following prefix: `did:komsco`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

# Method Specific Identifier <a name="identifier"></a>

The method specific identifier is composed of a Hex-encoded KOMSCO Identifier Number (KIN) (without a `0x` prefix).
```
komsco-did = "did:komsco:" + <first 16-bytes of public key w/ b58 encoding>
```
## Example <a name="example1"></a>

Example `komsco` DIDs:
```
did:komsco:123456789abcdefghi
```
# DID Document <a name="document"></a>

## Example <a name="example2"></a>
```
{
	"@context": "https://w3id.org/did/v1",
	"id": "did:komsco:123456789abcdefghi",
	
	"publicKey": [{
		"id": "did:komsco:123456789abcdefghi",
		"type": "Secp256k1VerificationKey2020",
		"publicKeyHash": "e3FA89810623759d53361a297305c391c8280e66"
	       },
	       {
		"id": "did:komsco:223456789abcdefghi",
		"type": "Secp256k1VerificationKey2020",
		"publicKeyHash": "5A0b54D5dc17e0AadC383d2db43B0a0D3E029c4c"
	       }
	],
	"authentication": [{
		"id": "did:komsco:323456789abcdefghi",
		"type": "Secp256k1VerificationKey2020",
		"publicKeyHex": "034f355bdcb7cc0af728ef3cc...59ab0f0b704075871aa"
	       }
	],
	"service": [{
		"id": "did:komsco:323456789abcdefghi",
		"type": "DIDHub",
		"serviceEndpoint": "https://komsco-did.herokuapp.com/"
	       }
	]
}
```

# JSON-LD Context Definition <a name="ld"></a>

The `komsco` method defines additional JSON-LD terms for the supported KOMSCO key types `MANAGEMENT`, and `SERVICE`.

The definition of the `komsco` JSON-LD context is:
```
{
	"@context":
	{
		"KomscoManagementKey": "{TBA}",
		"KomscoServiceKey": "{TBA}"
	}
}
```
# CRUD Operation Definitions <a name="crud"></a>

## Create <a name="create"></a>

In order to create a `komsco` DID, a KOMSCO Identity Manager (KIM) smart contract, and KOMSCO Service Manager (KSM) smart contract must be deployed on KOMSCO blockchain.
KIM is compliant with the * standard, and KSM is a default service key resolver.

`komsco` DID creation is done by submitting a transaction to the KIM smart contract invoking the following method:
```
function createIdentity(address recoveryAddress, address[] memory providers, address[] memory resolvers) public returns (uint KIN);
```
This will generate the corresponding id-string (KIN) and assign control to the caller address. Identities are denominated  by KINs, which are unique but otherwise uninformative.

## Read <a name="read"></a>

To construct a valid DID document from an `komsco` DID, the following steps are performed:

1. Determine  the KOMSCO network identifier ("mainnet", or "testnet"). If the DID contains no network identifier, then the default is "mainnet".
1. Invoke the `function getIdentity(uint KIN)` function to KIM for getting `MANAGEMENT` key.
1. For each returned key address, look up the associated key.
1. For each `MANAGEMENT` public key hash:
	1. Add a `publicKey` element of type `Secp256k1VerificationKey2020` and `KomscoManagementKey` to the DID Document.
    1. Add an `authentication` element of type `Secp256k1VerificationKey2020`, referencing this `publicKey`.
1. Invoke the `function getKeys(uint KIN)` function to KSM for getting  for `SERVICE` key.
1. For each `SERVICE` public key hash:
	1. Add a `publicKey` element of type `Secp256k1VerificationKey2020` and `KomscoServiceKey` to the DID Document.
	1. Add an `service` element of type  `Secp256k1VerificationKey2020`, referencing this `publicKey`.

Note: Service endpoints and other elements of a DID Document may be supported in future versions of this specification.

## Update <a name="update"></a>

The DID Document may be updated by invoking the relevant KSM smart contract functions as follows:
```
function addKey(address _key, uint KIN, bytes name)
function removeKey(address _key, uint KIN)
```
## Delete <a name="delete"></a>

Revoking the DID can be supported by executing a `destructIdentity` operation that is part of the KIM smart contract. This will remove the KIM and KSM's storage and code from the state, effectively marking the DID as revoked.
```
function destructIdentity(uint KIN)
```

# Security Considerations <a name="security"></a>

When a user creates and registers its own `komsco` DID in the KOMSCO blockchain, he (or she) can selectively register either recovery key or provider key. 
- Recovery key is a KOMSCO address (either an external account or smart contract) that can be used to recover lost Identities when you accidentally lose your private key. The recovery key must be set to a different value than the management key. It is safest to physically and logically allow a trusted third party, separate from the user, to archive the recovery key.
- The provider key is the KOMSCO address (external account or smart contract) that is authorized to be used on behalf of the management key. Since the provider key is allowed to operate in place of the management key, the provider key must be registered only when the user needs it, not at the time of distribution. Also, do not forget to delete the provider key when the proxy operation is complete.

# Privacy Considerations <a name="privacy"></a>

- The KOMSCO blockchain will have a claim/achievement structure in the next development phase. Claims are verifiable credentials, often consisting of a hash of personal information. GDPR protects pseudonymized data because of the "linkability" of an unreadable hash. Therefore, claims must be stored in a blockchain in such a way that personal information can not be inferred from hash-processed claims.

# References <a name="references"></a>
----------

 **[1]** https://w3c-ccg.github.io/did-spec/

 **[2]** https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust-fall2017/blob/master/topics-and-advance-readings/did-primer.md

 **[3]** https://www.iso.org/iso-8601-date-and-time-format.html

