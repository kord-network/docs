# KORD Stream

This document outlines the concept of a KORD stream which is used to
communicate the presence of KORD objects in the KORD network.

## Status of this Document

The KORD protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A KORD stream is a persistent, append-only sequence of links to KORD objects.

It is desirable to define a KORD stream as a sequence of links (rather than a
stream of the actual objects themselves) so that the source remains responsible
for the storage of the objects, with KORD services simply linking to those
objects rather than copying them around.

A KORD stream is typically appended to by a single participant of the network
and points to objects which that participant wishes to share with the network.

A concrete example of a KORD stream is a music label which pushes KORD objects
representing new music releases into the network.

## Rationale

Streams make it simple to construct indexes on top of the data: continuously
read objects from the stream, pick out the properties you want to index on and
then add the objects to your index graph. Indexes can also either be built in
real time or can be rebuilt at any point in the future.

Streams are also ideal for building data pipelines, incrementally merging and
transforming the data between service and API boundaries.

## Implementation

Given a KORD stream represents a mutable data structure (new objects are
continually added to the stream), but KORD objects are inherently immutable,
a KORD stream object is in fact just a pointer to the actual byte stream of
object links.

Implementors of KORD streams are free to choose the format of the byte stream
just as long as it is both persistent and append-only, and that the format
is defined in a property of the KORD stream object.

A reference implementation is still under development, but it will likely
reference an Ethereum ENS name which points at a Swarm hosted file containing
the concatenation of 32 byte swarm hashes for each object in the stream. Such
a stream can be subscribed to using an Ethereum log filter for ENS events, and
in the future with fast resource updates communicated via PSS.
