# KORD Aggregator

This document outlines the concept of a KORD aggregator, a KORD service which
combines multiple input KORD streams into a single output KORD stream.

## Status of this Document

The KORD protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

Due to the distributed and decentralised nature of the KORD network, there will
be many participants in the network maintaining their own KORD streams using
potentially different formats which contain related objects (for example many
news agencies publishing news articles).

To avoid the need to subscribe to many different streams and deal with many
different stream formats, a KORD aggregator will combine those streams into a
single KORD stream with a particular format.

## Incentives

KORD aggregators will be incentivised to both provide an efficient aggregation
of the input into a predictable output format, and to also aim to inject its
output into more useful upstream services. This will then attract participants
to use the aggregator in order to gain visibility in the network.

## Accountability

There are two things an aggregator can be held accountable for:

1. Not including input objects in the output.

    Given the owner of the input will have exchanged tokens to ensure their
    stream is included in the output, they will be able to challenge the
    service to provide a merkle proof that their objects are in fact included
    in the output. Failure to do so will mean the owner can claim collateral.

2. Including objects which do not appear in the input.

    All KORD objects have a [provable owner](object.md#base-properties), so the
    service can be held accountable if someone can provide a merkle proof that
    some object exists in the output stream whose owner is not also the owner
    of an input stream.
