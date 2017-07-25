# META Merger

This document outlines the concept of a META merger, a META service which
merges multiple linked META objects into a single META object.

## Status of this Document

The META protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A [META Linker](linker.md) can be employed to link objects together, but you
may also want to just go ahead and merge those linked objects together.

A META merger will take a collection of linked META objects and combine all of
their properties into a single META object, using some pre-defined ruleset for
determining how to merge conflicting properties (e.g. always prefer one
object's properties over another, concatenate the properties together etc.).

## Incentives

TODO

## Accountability

TODO: merged objects should only be trusted if signed by all owners of the
      linked objects?
