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
5. [CRUD Operation Definitions](#crud)
    1. [Create](#create)
    2. [Read](#read)
    3. [Update](#update)
    4. [Delete](#delete)
6. [Security Considerations](#security)
7. [Privacy Considerations](#privacy)
8. [References](#references)
 
# Abstract <a name="abstract"></a>
KOMSCO, as a government-designated national ID card/passports manufacturer and issuer in the Republic of Korea, has always strived hard to respond to the world's trends and to achieve protection against forgery and alteration for a long time.

KOMSCO Trusted platform is the blockchain based identity system. KOMSCO Trusted Platform provides services in the public sector such as safe payment and ID authentication as well as information protection and management services. 

This is version 0.1 of the KOMSCO Trusted platform DID Method Specification.

KOMSCO Decentralized Identifiers is a distributed identifier designed to provide a way for a community connected to the KOMSCO Trusted platform to uniquely identify an individual, organization, or IoT device. The role of a KOMSCO DID is to provide a service that supports id-authentication and personal information verification. 

The KOMSCO DID method specification conforms to the requirements specified in the Decentralized Identifiers (DIDs) v1.0 [**[1]**](https://w3c.github.io/did-core/), currently published by the W3C Credentials Community Group. 
For more information about DIDs and DID method specifications, please see the DID Spec and the DID Primer.
 
# DID Method Name <a name="name"></a>

The namestring that shall identify this DID method is: `komsco`

A DID that uses this method MUST begin with the following prefix: `did:komsco`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

# Method Specific Identifier <a name="identifier"></a>

The method specific identifier is composed of a first 16-bytes of public key w/ b58 encoding.
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
		"type": "Secp256k1VerificationKey2019",
		"owner": "did:komsco:223456789abcdefghi",
		"publicKeyHash": "e3FA89810623759d53361a297305c391c8280e66"
	       }],

	"authentication": [
		"id": "did:komsco:323456789abcdefghi",
	],
	"proof": [
		"id": "did:komsco:323456789abcdefghi",
	],
	"service": [{
		"type": "Schema",
		"serviceEndpoint": "https://komsco-did.herokuapp.com/"
	       }
	]
}
```

* @context - (required) This is a JSON-LD keyword that points to the semantic schema of the document. 

* id - (required) This is another JSON-LD keyword that signifies the value as a URL representing the surrounding object. Note that DIDs are URLs. We are using the lower 16 bytes of the initial public key as the identifier, since this will provide randomness tied to the document.

* publicKey - An array of PublicKey objects that represent the public keys that can validate this identity. Proofs on Verified Credentials and Proof Responses will reference the id of the key used to perform the signing.

* authentication - Another array of PublicKey objects that represents identities associated with this DID Document. PublicKeys defined exclusively in the authentication section will only be used for authenticating the association to this DID Document for update purposes. 

* service - An array of ServiceEndpoint objects that aid in service discovery. Service endpoints are optional; 

* proof - An array of Proof objects containing cryptographic signatures over the contents of the rest of the DID Document, together metadata about the signature. The smart contract verifies the signature before storing the DID Document on the ledger.

# CRUD Operation Definitions <a name="crud"></a>

## Create <a name="create"></a>

The DID Documents are cryptographically signed and sent to the KOMSCO Trusted Platform, where their signatures are checked and subsequently stored on the blockchain ledger. The ledger contains smart contracts that verify that the DID Documents are well-formed, that there are no ID conflicts, and checks the cryptographic signature against the rest of the DID Document, before writing it to the ledger.

## Read <a name="read"></a>

A GET request to the `https://komsco-did.herokuapp.com/v1/did/{did}` endpoint with a DID returns the DID Document corresponding to this DID. Behind the scenes, the KOMSCO Trusted Platform queries the ledger and returns the DID Document, if it exists.

## Update <a name="update"></a>

In the current implementation, DID Documents are immutable, and cannot be updated. Future enhancements will enable support for key rotations, revocations, and service changes

## Delete <a name="delete"></a>

In the current implementation, DID Documents are immutable, and cannot be deleted. Future enhancements will enable support for key rotations, revocations, and service changes


# Security Considerations <a name="security"></a>

When a user creates and registers its own `komsco` DID in the KOMSCO Trusted Platform, he (or she) can selectively register either recovery key or provider key. 

# Privacy Considerations <a name="privacy"></a>

- The KOMSCO Trusted platform will have a claim/achievement structure in the next development phase. Claims are verifiable credentials, often consisting of a hash of personal information. GDPR protects pseudonymized data because of the "linkability" of an unreadable hash. Therefore, claims must be stored in a blockchain in such a way that personal information can not be inferred from hash-processed claims.

# References <a name="references"></a>
----------

 **[1]** https://w3c-ccg.github.io/did-spec/

