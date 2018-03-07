# KORD Identity

This document outlines the concept of a KORD Identity. A KORD Identity is used
to both identify participants in the network using a cryptographic identifier
and to provide verifiable information about those participants.

## Status of this Document

The KORD protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A KORD Identity is a representation of a participant in the KORD network. It is
designed to be a self-sovereign identity with two functions:

- Referable unique identifier for a participant within the KORD network

- Enable web of trust mechanisms through inclusion and signing of external
  identifiers relating to the participant (eg. KORD-ID = [Spotify-ID, IPI,
  National ID, PGP key, Twitter handle])

## Terminology

- Custodian - an Ethereum address appointed by the KORD Identity owner and granted
  certain privileges in order to act on the owner's behalf.
- Identity Custody Scheme - system for controlling the ownership of KORD Identities.
- Identity Owner - an Ethereum address registered as the owner of a KORD Identity.
- Identity Signature Scheme - system for controlling the cryptographic signing of
  KORD Identity metadata.
- Smart Contract - a collection of code (functions) and data (state) that resides
  at a specific address on the Ethereum blockchain.
- Trust Item - a link between a cryptographic signature and a metadata item.
- `sha3()` - indicates `keccak256` hashing of the contents within parentheses.

## Core concepts

A KORD Identity MUST define:

- KORD-ID: A 32 byte ENS namehash
- CID: A content-addressed identifier

The KORD-ID is the UNIQUE identifier of the KORD Identity. It is IMMUTABLE. The KORD-ID is ONLY an index key, and MUST NOT be used for cryptographic purposes.

The CID MUST point to a valid KORD Object. The KORD Object MUST include a property "kord." The value of this property MUST be a data structure as defined in subsection "Core" under "Metadata."

The KORD Object CAN include an arbitrary number of metadata items. These define claims of auxiliary identifiers linked to the KORD Identity. These SHOULD define data structures as defined in subsection "Auxiliary" under "Metadata."

### Ownership and permissions

A KORD Identity MUST be owned by one single Ethereum address. The mapping of address to KORD Identity MUST be recorded on the Ethereum blockchain. Ownership of the KORD Identity MUST be enforced through the Identity Custody Scheme. The initial KORD Identity owner SHOULD be the transaction sender of the KORD Identity instantiation.

This ownership CAN be transferred to a different Ethereum address. Activation of this feature SHOULD be optional, and MUST include a user-modifiable grace period during which reverting to previous ownership is possible.

A KORD Identity owner CAN perform transfer of ownership. A KORD Identity owner CAN appoint a number of Ethereum addresses to act as *custodians*. These CAN through a vote perform transfer of ownership. A vote MUST require a message signed by a respective private key. The *minimum number of addresses* that must make up a custodian role, and *minimum ratio of votes* required to execute a transfer CAN ONLY be determined by the KORD Identity owner.

### Implementation


The KORD Identity uses ENS as backend for storage and retrieval, interfaced by the Identity and IdentityResolver contracts.

The Identity contract is upgradeable and MUST be registered in the IdentityController contract. As the IdentityController instantiates all permanent storage, and guarantees that the contents of these cannot be altered between upgrades. Therefore only the instance of the Identity contract that has been registered with the IdentityController can be considered valid for use.

The IdentityResolver contract is modelled on the ENS PublicResolver and used to set and resolve the CID content hash associated with an ENS node (KORD-ID). The content of a node can ONLY be set by the owner of that node.

In the following we assume the KORD-ID to be `sha3("foo")`.

#### Add a new identity

```
Identity.register(bytes32 kordIdHash, address owner);
```

This will make `owner` the owner of the KORD-ID, along with the ENS node `namehash("foo.id.kord")`. If the KORD-ID has already been registered, the method will throw an exception.

#### Add metadata for identity

```
IdentityResolver.setContent(bytes32 node, bytes32 hash);
```

sets the CID content hash `hash` as the metadata for the node `namehash("foo.id.kord")`

#### Retrieve metadata for identity

```
IdentityResolver.content(bytes32 node);
```

gets the CID content hash of the metadata for the node `namehash("foo.id.kord")`

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

KORD Identity metadata MUST constitute two parts; Core and Auxiliary. Core metadata is REQUIRED and can be defined only once. The Auxiliary metadata is OPTIONAL, and an arbitrary number of Auxiliary metadata items may be defined.

Metadata may ONLY be changed by the KORD Identity owner.

#### Core

The following properties are REQUIRED:

```
id		: KORD-ID // reverse point to KORD-ID this metadata belongs to
name		: string // name whom identity represents
parent		: CID // pointer to previous version of this metadata, initialised to 0x0
```
#### Auxiliary

Auxiliary metadata items MUST be children of a container named `aux` in the root level of the KORD-ID Meta Object.

An Auxiliary metadata item SHOULD be interpreted as key-value pairs:

- key: subpath name
- value: the data stored in the subpath.

#### Example

Given we have the following files stored on disk:

kordid/kord:
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

kordid/aux/rsa:
```
AAAAB3NzaC1yc2EAAAADAQABAAABAQDH52nZuslWyqiAhxRUUVgveBe3hjqlq1cPFo8Ld9BpIe/gBAYffCjXui+cP9ifaHgJP611QBsTXOdhQouwc3bWM1U72xHBMNt+vniCI6xkSyzc/jKE2SQbSPZUIb/z6cBmpi7vpIRw1+D1Njqh0ldqjnJ9TiFqYSNMCSBgKPeg6sb7249Xhj34Km5+HqmRkBnEmCmoHAtCIx51ZeJfANH/uuAjS/II7w+K+6f5x4jyPhLyeu5xOmAXF6MvqC+P/1KAsULL6tw/Qk6d85C5Hy8QKJYwukl9+f0O7oV8aIVKxe6VtpXM3I09FFwR3/iBAdO64Cel4YhepY28DofU3Mrz
```

kordid/aux/ecdsa:
```
0x040b81120b647d6c1e9e123a50c896a05ea47c660649e72e950b085c2d0d887db83a188f9c4bd06e1067b2c133a777217552a85ac2ba9004885c1ae37976c9abd9
```

kordid/aux/foo:
```
42
```

kordid/aux/bar:
```
baz
```

kordid/aux/xyzzy:
```
022fe8c3-e8de-49e0-8cd5-59ed02ca45c7
```

We can use *swarm recursive upload* to create a manifest that satisfies the KORD Identity metadata constraints:

```
BZZHOST="127.0.0.1:8500" KORDBZZ=`swarm --manifest=true --recursive up kordid`; curl "http://$BZZHOST/bzzr:/$KORDBZZ?content_type=text/plain"
```

This will yield the following manifests on hash d10945ebf25cdd0428df6b8334438e16bdc533add4198fede466e1bf2d18a40e:

```
{"entries":[{"hash":"9d6b2c490c2192675ac3511061a54e803f50505014c12d8687356645cb4d3457","path":"aux/","contentType":"application/bzz-manifest+json","mod_time":"0001-01-01T00:00:00Z"},{"hash":"f0af87f9ffd3bfc5fa8c54fcbf0f659edc572d49052570dc9e56d29c453abc9a","path":"kord","mode":420,"size":227,"mod_time":"2017-09-04T12:31:37+02:00"}]}

{"entries":[{"hash":"0d615ea5f50d0eae42b3ee071f473e5c0b152a8a7c361c88a42d21bdc835c0cc","path":"bar","mode":420,"size":4,"mod_time":"2017-09-04T12:32:28+02:00"},{"hash":"d9fde9bd2960db437a3d258fee53f6e1dde2dbea07ebbb5e56149b8e7d3afa5f","path":"ecdsa","mode":420,"size":133,"mod_time":"2017-09-04T12:32:07+02:00"},{"hash":"d3c5856a51886fa58e3add827837c1a708b0cbe3df8763f646a6f7eb5956dd4a","path":"foo","mode":420,"size":3,"mod_time":"2017-09-04T12:32:20+02:00"},{"hash":"46b543158c5d39c81964ee0100d1d64dcddf7f699b1d5f6dd10b110a69325bcc","path":"rsa","mode":420,"size":373,"mod_time":"2017-09-04T12:31:54+02:00"},{"hash":"5032864c5d1ad9455a6b0ae68d87409b8f5f2957e222dd657f607fc2ebe593d5","path":"xyzzy","mode":420,"size":37,"mod_time":"2017-09-04T12:32:43+02:00"}]}
```

The hash of the "kord" file represents the CBOR representation of the above JSON, as described in [KORD Object encoding](https://github.com/kord-network/docs/blob/master/concepts/object.md#object-encoding).

## Services

### Signing

The KORD Network SHOULD provide a mechanism of KORD Identity trust.

A Trust Item in KORD Identity SHOULD be a detached cryptographic signature of a metadata item.

Both Core and Auxiliary metadata items CAN be signed.

Mapping between signature and metadata item MUST be enforced by the Identity Signature Contract.

Adding a signature to a metadata item MUST NOT require specific privilieges.

#### Implementation

A signature of a Core metadata item SHOULD be a detached cryptographic signature of the KORD-ID.

A signature of an Auxiliary metadata SHOULD be a detached cryptographic signature of the content hash of value in the key-value pair.

A signature MUST be registered with the Identity Signature Contract to be available on the KORD Network. Signatures are mapped to:

- KORD-ID of the owner of the metadata item
- key of the kord item

If the key is "kord", the signature is a global signature for the KORD-ID itself, and MUST be a signature of the KORD-ID hash itself.

##### Adding a signature

```
setSignature(bytes32 signerKordId, bytes32 targetKordId, bytes32 metadataItemIdHash, bytes32 metadataHash, bytes32 signatureHash)
```

`metadataHash` content identified by `metadataItemIdHash` belonging to `targetKordId` is signed by `signerKordId`. The content hash of the signature is `signatureHash`.

##### Retrieving signatures

```
count = getSignatureCount(bytes32 targetKordId, bytes32 metadataItemIdHash)
```

gets the number of signatures registered for metadata item identified by `metadataItemIdHash`, owned by `targetKordId`

```
signer, metadataHash, signatureHash = getSignatureByIndex(bytes32 targetKordId, bytes32 metadataItemIdHash, uint32 idx)
```

gets the `idx` signature from the array of signatures for the metadata item identified by `metadataItemIdHash` owned by `targetKordId`.

#### Example

Given a KORD-ID as defined by the subsection "Metadata/Example", a detached signature for the auxiliary metadata item with key "foo" could be generated using `gpg` and uploaded to swarm as follows:

```
SIGNSWARMHASH=`gpg -abu <gpg-id> -o- kordid/aux/foo | swarm --stdin up`
```

This signature would then be registered as follows:

```
setSignature(sha3(kordIdSigner), sha3(kordIdTarget), sha3("aux/foo"), "0xd3c5856a51886fa58e3add827837c1a708b0cbe3df8763f646a6f7eb5956dd4a", $SIGNSWARMHASH)

```

The global kord id signature would be generated and uploaded to swarm as follows.

```
SIGNSWARMHASH=`echo -n sha3("aux/foo") | gpg -abu <gpg-id> -o-  | swarm --stdin up`
```

And registered in the same manner as above.

### Verification

The KORD Network SHOULD NOT be opinionated concerning the quality of identity authenticity. The role of the network SHOULD BE to present evidence for third parties to evaluate. The network COULD however offer some tools to make retrieval and reporting of evidence as clear as possible.

The KORD Identity metadata items under the *key* subfolder SHOULD be used as reference for network agents to map cryptographic signatures to KORD-IDentities.

## Security Considerations

The custodian scheme is intended to lessen the consequences of private key loss. It is NOT to be considered as a replacement for private key security. It is NOT a "forgotten password" scheme. Because the scheme depends on trusting individuals other than the key owner, there is always a certain potential for misuse (conspiracies, force grace period expiries).

The scheme is NOT intended to subvert key theft. If the key is compromised, the perpetrator will in effect be able to overthrow custodianship and transfer ownership to a different account entirely.

## Incentives

TODO: outline incentives

## Accountability

TODO: outline accountability
