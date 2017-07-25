# META Concepts and Specifications

This repository defines concepts and specifications of the META protocol.

## Status

The META protocol is at an early stage of development, expect the documents in
this repository to change frequently during this early phase.

## Concepts

The primary aim of the META protocol is to provide the tooling for building
large scale, decentralised databases describing real world entities, where
individual participants are both economically incentivised to provide useful,
efficient services and are held accountable for any wrong doing.

In order to achieve this, we introduce the following concepts (follow the links
for each concept for a more detailed description):

* [META Object](concepts/object.md) - a set of properties representing an
  arbitrary entity

* [META Stream](concepts/stream.md) - a persistent, append-only sequence of
  content-addressed links to META objects

* [META Service](concepts/service.md) - a participant in the network who is
  economically incentivised to provide a service, and can be held accountable
  for its actions

* [META Aggregator](concepts/aggregator.md) - a META service which combines
  multiple input META streams into a single output META stream

* [META Indexer](concepts/indexer.md) - a META service which reads META objects
  from a META stream and indexes them based on one or more properties

* [META Linker](concepts/linker.md) - a META service which creates links
  between META objects

* [META Merger](concepts/merger.md) - a META service which combines multiple
  META objects into a single META object

* [META Identity](concepts/identity.md) - TODO define META Identity

## Implementations

* [go-meta](https://github.com/meta-network/go-meta) - The Go META library

## Tools

* [meta-tools](https://github.com/meta-network/meta-tools) - Tools for
  converting structured data (e.g. XML documents) into META object graphs

## Contribute
Please follow our contribution. Your help is appreciated, thanking you.
[Contribution guide](CONTRIBUTING.md)

## License

MIT © JAAK MUSIC LTD
