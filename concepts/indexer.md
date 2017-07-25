# META Indexer

This document outlines the concept of a META indexer, a META service which
provides efficient access to data stored within the META network.

## Status of this Document

The META protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

As outlined in the [META Stream](stream.md) concept document, the presence of
objects within the META network are communicated via persistent, append-only
streams. If you want to find a particular object in the network which you know
has been communicated in a particular stream, then you have the option of
incrementally reading through that stream until you find that object.

Due to the append-only nature of streams however, this task will get ever less
efficient over time as the stream grows in size. What you need is an index
which acts as a lookup table into the stream, so when looking for an object
with a particular property value, you instead retrieve a link to the object
directly from the the index object.

## Implementation

A META indexer provides such an index object by reading objects from a stream
and assigning particular property values of those objects as properties of the
index object which link to those objects.

As a concrete example, imagine a stream of objects representing 1,000 people,
and you want to query them based on gender. The indexer would construct a
gender index object with two properties `male` and `female`, the values of
which would point at two META streams, one containing just males and the other
just females.

## Incentives

TODO: outline incentives (similar to aggregator)

## Accountability

TODO: outline accountability (similar to aggregator)
