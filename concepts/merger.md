# KORD Merger

This document outlines the concept of a KORD merger, a KORD service which
merges multiple linked KORD objects into a single KORD object.

## Status of this Document

The KORD protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A [KORD Linker](linker.md) can be employed to link objects together, but you
may also want to just go ahead and merge those linked objects together.

A KORD merger will take a collection of linked KORD objects and combine all of
their properties into a single KORD object, using some pre-defined ruleset for
determining how to merge conflicting properties (e.g. always prefer one
object's properties over another, concatenate the properties together etc.).

## Incentives

TODO

## Accountability

TODO: merged objects should only be trusted if signed by all owners of the
      linked objects?
