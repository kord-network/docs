# KORD Linker

This document outlines the concept of a KORD linker, a KORD service which
creates links between KORD objects based on some pre-defined rules.

## Status of this Document

The KORD protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

A major issue with distributed databases of objects coming from many different
sources is identifying objects which describe the same thing but in subtly
different ways.

For example, consider a person named "Alexander Smith" whom is commonly known
as "Alex Smith", and objects using either one of those first names. Because
these objects are not identical, they will have different identifiers and so
without further action, there will be two disjoint graphs of objects related
to this person.

In this case, a KORD linker will be employed with a rule that is "link all
objects which reference this person as either Alex or Alexander".

## Incentives

TODO

## Accountability

TODO
