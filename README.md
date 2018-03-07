# KORD Concepts and Specifications

KORD is both a network of distributed, decentralised systems (the KORD
network), and a protocol which governs how those systems communicate
(the KORD protocol).

This repository defines concepts and specifications of KORD.

## Status

KORD is at an early stage of development, expect the documents in
this repository to change frequently during this early phase.

## Concepts

The primary aim of KORD is to provide the tooling for building
large scale, decentralised databases describing real world entities, where
individual participants are both economically incentivised to provide useful,
efficient services and are held accountable for any wrong doing.

In order to achieve this, we introduce the following concepts (follow the links
for each concept for a more detailed description):

* [KORD Object](concepts/object.md) - a set of properties representing an
  arbitrary entity

* [KORD Stream](concepts/stream.md) - a persistent, append-only sequence of
  content-addressed links to KORD objects

* [KORD Service](concepts/service.md) - a participant in the network who is
  economically incentivised to provide a service, and can be held accountable
  for its actions

* [KORD Aggregator](concepts/aggregator.md) - a KORD service which combines
  multiple input KORD streams into a single output KORD stream

* [KORD Indexer](concepts/indexer.md) - a KORD service which reads KORD objects
  from a KORD stream and indexes them based on one or more properties

* [KORD Linker](concepts/linker.md) - a KORD service which creates links
  between KORD objects

* [KORD Merger](concepts/merger.md) - a KORD service which combines multiple
  KORD objects into a single KORD object

* [KORD Identity](concepts/identity.md) - a KORD Identity is used to both identify
participants in the network using a cryptographic identifier and to provide verifiable
information about those participants.

* [KORD Courtroom](concepts/courtroom.md) - a suite of smart contracts for KORD service
agreements, between service provider and client, which can be used to claim collateral
if a KORD service is found guilty of wrong doing.

* KORD Token - KORD tokens are used in automated exchange between services and providers
on the network. Tokens can also be used explicitly by applications that implement the
KORD protocol and seek to create or retrieve KORD objects. Further information to follow.


## Implementations

* [go-kord](https://github.com/kord-network/go-kord) - The Go KORD library

## Contribute
We welcome community contributions, please see the [contributing guide](CONTRIBUTING.md), thanking you.

## License

MIT © JAAK MUSIC LTD
