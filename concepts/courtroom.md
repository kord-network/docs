# META Courtroom

This document outlines the concept of a META courtroom which is a suite of smart contracts which can be used to claim collateral if a META service is found guilty of wrong doing.

## Status of this Document

The META protocol is at an early stage of development, expect this document to
change frequently during this early phase.

## Outline

The META Courtroom aims to provide a framework for META services and service
clients which is used for conflict resolution in relation to the service being provided.
The service provider specifies their service offering by tendering a process
in which they can be held accountable for not complying with their quality guarantees.
Provider swears to this promise by depositing a collateral which they stake to lose
in case of foul play.

If some service was not provided as promised, clients can open a case and start
a litigation process. In case the evidence is available and can prove the accusation,
the service provider can be held accountable by an automatic self-enforcing punitive
measure.

Such accountability is crucial in the design of scalable decentralised service economies.

The META Courtroom concept is based on [swarm swap swear and swindle](http://swarm-gateways.net/bzz:/theswarm.eth/ethersphere/orange-papers/1/sw%5E3.pdf),
a generalised framework for incentivised service provision.

The courtroom framework will offer a generic interface for handling cases,
registering clients and services and submitting new cases where each service can use
and set its own specific game rules.
e.g which evidence types a client should submit for a case, grace periods for submitting
evidences, the compensation amount for the case of a valid case...


## Implementation

The courtroom framework includes a solidity smart contracts suite, an ABI interface
specification and tools to interact with these contracts.

A courtroom suite includes a generic contract which knows how to open a case and conduct a trial.
An abstract trial is described by a finite state automaton states of which correspond to stages of litigation and
the transitions are labeled by various outcomes of evaluating evidence submitted to the respective expert witnesses
associated with the stage.
The `Swindle` contact just orchestrates the transition through the stages calling the witness contracts, handles grace periods to control the deadline for submitting evidence (challenges or their refutations), reach a guilty/non-guilty verdict accordingly.

The specific service contract defines the rules through describing the actual stages by virtue of assigning
particular expert witnesses to trial stages: these witness contracts are supposed to be giving a testimony
evaluating evidence submitted to them. The request response process between the judge and the witnesses can
be easily standardized so all witness contracts implement the same interface.

Each expert witness may need to submit different types of evidence. Thus, this abstraction allows us to handle polymorphic evidence.

An early phase reference implementation of a courtroom for a simple mirror game
can be found [here](https://github.com/ethersphere/swap-swear-and-swindle/tree/features/gh_3)
