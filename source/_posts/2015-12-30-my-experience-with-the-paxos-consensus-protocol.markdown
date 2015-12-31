---
layout: post
title: "My experience with the Paxos consensus protocol"
date: 2015-12-30 12:40:43 -0500
comments: true
categories:
- Distributed Systems
---

Paxos is a protocol created by Leslie Lamport that allow distributed nodes to reach agreement on mutually shared state. What makes this protocol powerful is its ability to preserve linearizability across node failures and network partitions. I had the fortunate (or not?) experience implementing a paxos library for a distributed systems project. Here I will talk about, from my perspective, what "preserving linearizability" mean and how Paxos works at an implementation level.

<!-- more -->

## Linearizability

To appreciate what Paxos does, we must first understand what linearizability is. The term linearizability is usually brought up when we talk about concurrent operations performed on objects. Although linearizability could be said about transactions as well, here I will just stick to linearizability as applied to operations on objects. For example, in a simple key/value store (our object) which we can apply operations Put, Append and Get, we could say that concurrent executions of these ops are linearizable.

Linearizability requires satisfying the following 2 constraints:
* Each node/thread preserves the sequential operations order as given by execution of the program
* Across node/thread (global property)
