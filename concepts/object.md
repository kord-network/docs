# META Object

This document outlines the concept of a META object which is used to represent
all data in the META network.

## Status of this Document

The META protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A META object is a set of properties representing an arbitrary entity, and all
data in the META network is represented by META objects.

The value of a META object's properties can either be an arbitrary string or a
link to another META object (links are discussed further in the
[Object Linking](#object-linking) section).

As a concrete example, consider a social network which uses the META network
to maintain a global database of users and their connections. In this example,
users would be represented by META objects with string-valued properties like
`name`, `gender` and `dateOfBirth`, and their connections would be represented
by a `friends` property whose value is a link to another META object which
represents a set of users.

## Object Identifier

In order to reference and retrieve objects in the META network, they require
a unique identifier.

### Traditional Identifiers

In a non-distributed database, it is trivial to assign unique identifiers, just
use an increasing sequence number for each object (e.g. 1, 2, 3, ...).

This is slightly less trivial in a distributed database as you may end up
assigning the same identifier to multiple objects, and a common technique is to
use a [Universally Unique Identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier)
(UUID), but this identifier has no relation to the actual data, so two
identical objects in the database could end up with two distinct identifiers.

### META Identifiers

Given databases in the META network are not only distributed, but also
decentralised, trying to co-ordinate to ensure unique identifiers are assigned
to objects is challenging, but there is a technique which is easy to implement
and has some desired properties: use the cryptographic hash of some canonical
representation of the object as the identifier, for example:

```
id = sha256(canonicalise(object))
```

This identifier has a number of useful properties:

#### Uniqueness

Using a cryptographic hash function which is considered collision resistant
means that we can be confident that object identifiers will be unique.

#### Deduplication

If two objects are identical, then they will have identical identifiers meaning
they only need to be stored once, avoiding duplication.

#### Integrity Checking

Because the identifier is derived from the object itself, on retrieving the
object from the network it can be integrity checked by generating the
identifier of the object and checking it matches what was requested.

## Object Encoding

In order to store and transfer objects in the META network, we need to be able
to encode them into some binary format, and we also need this
format to be canonical so that object identifiers are generated consistently.

At this early stage, META has adopted the same canonical representation as the
[IPLD project](https://ipld.io/) which is
[canonicalised CBOR with tags](https://github.com/ipld/specs/tree/master/ipld#canonical-format).

In the future, it will be desirable to use a tree like representation which
supports efficient lookup of object properties without decoding the entire
object, and support merkle proofs with a small resolution so part of an object
can be loaded but still integrity checked against the identifier by also
receiving an appropriate merkle proof.

TODO: expand on the above paragraph.

## Object Linking

As well as storing objects for later retrieval, the META network also provides
the ability to link objects together, thus forming graphs of objects which can
be traversed.

Because META objects can have abitrary property names and values, links to
other objects are made explicit in the underlying object encoding so that
there is no debate whether a property is a string or a link.

With the current IPLD CBOR representaion, links are encoded using a special
CBOR tag (see [RFC 7049 section 2.4](https://tools.ietf.org/html/rfc7049#section-2.4)).

## Base properties

All META objects have a base set of properties:

* `source` - the META Identity which is the source of the information
  represented by the object
* `signature` - a cryptographic signature proving the object's source
* `createdAt` - the time the object was created

TODO: discuss and expand upon this base set of properties
