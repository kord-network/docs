# META Identity

This document outlines the concept of a META Identity. A META Identity is used
to both identify participants in the network using a cryptographic identifier
and to provide verifiable information about those participants.

## Status of this Document

The META protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A META Identity is a representation of a participant in the META network. It is
designed to be a self-sovereign identity with two functions:

- Referable unique identifier for a participant within the META network

- Enable web of trust mechanisms through inclusion and signing of external
  identifiers relating to the participant (eg. META-ID = [Spotify-ID, IPI,
  National ID, PGP key, Twitter handle])

## Terminology

*TODO*: complete definitions of terms used in this document

- Custodian
- Smart Contract
- Identity Owner
- META-ID
- Identity Signature Scheme
- Identity Custody Scheme
- Trust Item - a detached cryptographic signature of a metadata item

sha3() indicates keccak256 hashing of the contents within parentheses.

## Core concepts

A META Identity MUST define:

- META-ID: A 32 byte ENS namehash
- CID: A content-addressed identifier

The META-ID is the UNIQUE identifier of the META Identity. It is IMMUTABLE. The META-ID is ONLY an index key, and MUST NOT be used for cryptographic purposes.

The CID MUST point to a valid META Object. The META Object MUST include a property "meta." The value of this property MUST be a data structure as defined in subsection "Core" under "Metadata."

The META Object CAN include an arbitrary number of metadata items. These define claims of auxiliary identifiers linked to the META Identity. These SHOULD define data structures as defined in subsection "Auxiliary" under "Metadata."

### Ownership and permissions

A META Identity MUST be owned by one single Ethereum address. The mapping of address to META Identity MUST be recorded on the Ethereum blockchain. Ownership of the META Identity MUST be enforced through the Identity Custody Scheme. The initial META Identity owner SHOULD be the transaction sender of the META Identity instantiation.

This ownership CAN be transferred to a different Ethereum address. Activation of this feature SHOULD be optional, and MUST include a user-modifiable grace period during which reverting to previous ownership is possible.

A META Identity owner CAN perform transfer of ownership. A META Identity owner CAN appoint a number of Ethereum addresses to act as *custodians*. These CAN through a vote perform transfer of ownership. A vote MUST require a message signed by a respective private key. The *minimum number of addresses* that must make up a custodian role, and *minimum ratio of votes* required to execute a transfer CAN ONLY be determined by the META Identity owner.

### Implementation


The META Identity uses ENS as backend for storage and retrieval, interfaced by the Identity and IdentityResolver contracts.

The Identity contract is upgradeable and MUST be registered in the IdentityController contract. As the IdentityController instantiates all permanent storage, and guarantees that the contents of these cannot be altered between upgrades. Therefore only the instance of the Identity contract that has been registered with the IdentityController can be considered valid for use.

The IdentityResolver contract is used to set and resolve the CID content hash associated with an ENS node (META-ID). The content of a node can ONLY be set by the owner of that node.

In the following we assume the META-ID to be `sha3("foo")`.

#### Add a new identity

```
Identity.register(bytes32 metaidhash, address owner);
```

This will make `owner` the owner of the META-ID, along with the ENS node `namehash("foo.id.meta")`. If the META-ID has already been registered, the method will throw an exception.

#### Add metadata for identity

```
IdentityResolver.setContent(bytes32 node, bytes32 hash);
```

sets the CID content hash `hash` as the metadata for the node `namehash("foo.id.meta")`

#### Retrieve metadata for identity

```
IdentityResolver.content(bytes32 node);
```

gets the CID content hash of the metadata for the node `namehash("foo.id.meta")`

#### Retrieve the current valid Identity contract

```
IdentityController.current();
```

gets the address of the current valid Identity Contract

#### Not yet implemented

* Identity Custody Scheme contracts
* events for invalid attempts of changes to identity
* events for initiated change identity owner vote
* events for notifying identity monitors to request refreshing their indexing (used for updating public keys) ...

### Metadata

META Identity metadata MUST constitute two parts; Core and Auxiliary. Core metadata is REQUIRED and can be defined only once. The Auxiliary metadata is OPTIONAL, and an arbitrary number of Auxiliary metadata items may be defined.

Metadata may ONLY be changed by the META Identity owner.

#### Core

The following properties are REQUIRED:

```
id		: META-ID // reverse point to META-ID this metadata belongs to
name		: string // name whom identity represents
parent		: CID // pointer to previous version of this metadata
```
#### Auxiliary

Auxiliary metadata items MUST be grouped in two subfolders; *key* and *aux*.

All items in the *key* folder SHOULD be considered as public keys used by the META Identity for cryptographic signing purposes.

All items in the *aux* folder SHOULD be considered as external identifiers that have no cryptographic function.

An Auxiliary metadata item SHOULD be interpreted as key-value pairs:

- key: subpath name
- value: the data stored in the subpath.

#### Example

Given we have the following files stored on disk:

metaid/meta:
```
{
	"source":"0x0",
	"signature":"0x0",
	"createdAt":1501790362,
	"id":"0x1234567890123456789012345678901234567890",
	"name":"Guybrush McKracken",
	"parent":"0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"
}
```

metaid/key/rsa:
```
AAAAB3NzaC1yc2EAAAADAQABAAABAQDH52nZuslWyqiAhxRUUVgveBe3hjqlq1cPFo8Ld9BpIe/gBAYffCjXui+cP9ifaHgJP611QBsTXOdhQouwc3bWM1U72xHBMNt+vniCI6xkSyzc/jKE2SQbSPZUIb/z6cBmpi7vpIRw1+D1Njqh0ldqjnJ9TiFqYSNMCSBgKPeg6sb7249Xhj34Km5+HqmRkBnEmCmoHAtCIx51ZeJfANH/uuAjS/II7w+K+6f5x4jyPhLyeu5xOmAXF6MvqC+P/1KAsULL6tw/Qk6d85C5Hy8QKJYwukl9+f0O7oV8aIVKxe6VtpXM3I09FFwR3/iBAdO64Cel4YhepY28DofU3Mrz
```

metaid/key/ecdsa:
```
0x040b81120b647d6c1e9e123a50c896a05ea47c660649e72e950b085c2d0d887db83a188f9c4bd06e1067b2c133a777217552a85ac2ba9004885c1ae37976c9abd9
```

metaid/aux/foo:
```
42
```

metaid/aux/bar:
```
baz
```

metaid/aux/xyzzy:
```
022fe8c3-e8de-49e0-8cd5-59ed02ca45c7
```

We can use *swarm recursive upload* to create a manifest that satisfies the META Identity metadata constraints:

```
BZZHOST="127.0.0.1:8500" METABZZ=`swarm --manifest=true --recursive up metaid`; curl "http://$BZZHOST/bzzr:/$METABZZ?content_type=text/plain"
```

This will yield the following manifests on hash df32102800885d63dd0583da13df50eef9f90dc78a0ef4e588005a9f601b2fa1:

```
{"entries":[{"hash":"90c16d4d04bbadca87682c6ea682d8d9c44c5ee3a7f62077f119c072d7c35a9e","path":"aux/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},{"hash":"a7ec7dd31842ed0f4bacd5f7add9e4531d9917972aa17a78e57c8566ba1a3f6f","path":"key/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},{"hash":"f0af87f9ffd3bfc5fa8c54fcbf0f659edc572d49052570dc9e56d29c453abc9a","path":"meta","mode":420,"size":187,"mod_time":"2017-08-08T10:02:03+02:00"}]}

{"entries":[{"hash":"d9fde9bd2960db437a3d258fee53f6e1dde2dbea07ebbb5e56149b8e7d3afa5f","path":"ecdsa","mode":420,"size":133,"mod_time":"2017-08-08T09:43:43+02:00"},{"hash":"ac56abfa0648d55d2277c267464f1ceea0606d59249c24b3db85f75b494ad7a3","path":"rsa","mode":420,"size":374,"mod_time":"2017-08-08T09:42:08+02:00"}]}

{"entries":[{"hash":"0d615ea5f50d0eae42b3ee071f473e5c0b152a8a7c361c88a42d21bdc835c0cc","path":"bar","mode":420,"size":4,"mod_time":"2017-08-08T09:44:02+02:00"},{"hash":"d3c5856a51886fa58e3add827837c1a708b0cbe3df8763f646a6f7eb5956dd4a","path":"foo","mode":420,"size":3,"mod_time":"2017-08-08T09:43:53+02:00"},{"hash":"5032864c5d1ad9455a6b0ae68d87409b8f5f2957e222dd657f607fc2ebe593d5","path":"xyzzy","mode":420,"size":37,"mod_time":"2017-08-08T09:44:27+02:00"}]}
```

The hash of the "meta" file represents the CBOR representation of the above JSON, as described in [META Object encoding](https://github.com/meta-network/docs/blob/master/concepts/object.md#object-encoding).

## Services

### Signing

The META Network SHOULD provide a mechanism of META Identity trust.

A Trust Item in META Identity SHOULD be a detached cryptographic signature of a metadata item.

Both Core and Auxiliary metadata items CAN be signed.

Mapping between signature and metadata item MUST be enforced by the Identity Signature Contract.

Adding a signature to a metadata item MUST NOT require specific privilieges.

#### Implementation

A signature of a Core metadata item SHOULD be a detached cryptographic signature of the META-ID.

A signature of an Auxiliary metadata SHOULD be a detached cryptographic signature of the content hash of value in the key-value pair.

A signature MUST be registered with the Identity Signature Contract to be available on the META Network. Signatures are mapped to:

- META-ID of the owner of the metadata item
- key of the meta item

If the key is "meta", the signature is a global signature for the META-ID itself, and MUST be a signature of the META-ID hash itself.

##### Adding a signature

```
setSignature(bytes32 signermetaid, bytes32 targetmetaid, bytes32 metadataitemidhash, bytes32 metadatahash, bytes32 signaturehash)
```

`metadatahash` content identified by `metadataitemidhash` belonging to `targetmetaid` is signed by `signermetaid`. The content hash of the signature is `signaturehash`.

##### Retrieving signatures

```
count = getSignatureCount(bytes32 targetmetaid, bytes32 metadataitemidhash)
```

gets the number of signatures registered for metadata item identified by `metadataitemidhash`, owned by `targetmetaid`

```
signer, metadatahash, signaturehash = getSignatureByIndex(bytes32 targetmetaid, bytes32 metadataitemidhash, uint32 idx)
```

gets the `idx` signature from the array of signatures for the metadata item identified by `metadataitemidhash` owned by `targetmetaid`.

#### Example

Given a META-ID as defined by the subsection "Metadata/Example", a detached signature for the auxiliary metadata item with key "foo" could be generated using `gpg` and uploaded to swarm as follows:

```
SIGNSWARMHASH=`gpg -abu <gpg-id> -o- metaid/aux/foo | swarm --stdin up`
```

This signature would then be registered as follows:

```
setSignature(sha3(metaid_signer), sha3(metaid_target), sha3("aux/foo"), "0xd3c5856a51886fa58e3add827837c1a708b0cbe3df8763f646a6f7eb5956dd4a", $SIGNSWARMHASH)

```

The global meta id signature would be generated and uploaded to swarm as follows.

```
SIGNSWARMHASH=`echo -n sha3("aux/foo") | gpg -abu <gpg-id> -o-  | swarm --stdin up`
```

And registered in the same manner as above.

### Verification

The META Network SHOULD NOT be opinionated concerning the quality of identity authenticity. The role of the network SHOULD BE to present evidence for third parties to evaluate. The network COULD however offer some tools to make retrieval and reporting of evidence as clear as possible.

The META Identity metadata items under the *key* subfolder SHOULD be used as reference for network agents to map cryptographic signatures to META-IDentities.

## Security Considerations

The custodian scheme is intended to lessen the consequences of private key loss. It is NOT to be considered as a replacement for private key security. It is NOT a "forgotten password" scheme. Because the scheme depends on trusting individuals other than the key owner, there is always a certain potential for misuse (conspiracies, force grace period expiries).

The scheme is NOT intended to subvert key theft. If the key is compromised, the perpetrator will in effect be able to overthrow custodianship and transfer ownership to a different account entirely.

## Incentives

TODO: outline incentives

## Accountability

TODO: outline accountability
