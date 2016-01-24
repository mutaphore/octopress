---
layout: post
title: "Paxos consensus protocol, part 1"
date: 2015-12-30 12:40:43 -0500
comments: true
published: true
categories:
- Distributed Systems
---

Paxos is a protocol created by Leslie Lamport that allow distributed nodes to reach agreement on mutually shared state. You can read the original paper [here][leslie]. What makes this protocol powerful is its ability to preserve linearizability across node failures and network partitions. I had the fortunate experience of implementing a paxos library for a distributed systems school project. Here I will talk about, from my perspective, what "preserving linearizability" mean and how Paxos works at an implementation level.

<!-- more -->

## Linearizability

To see what Paxos does for us, we must first understand the concept of linearizability. The term linearizability is usually brought up when we talk about concurrent operations performed on objects. Although linearizability could be said about transactions as well, here I will just stick to linearizability as applied to operations on objects. For examples provided here, we will use a simple key-value store (the object) which we can apply operations Put, Append and Get. We will see examples that tell us whether concurrent executions of these ops are linearizable. Linearizability requires satisfying the following 2 constraints with regards to concurrent operations:

* Each node preserves the operations order given by sequential execution of the program (local property).
* Completion-to-invocation order between operations **across** nodes is preserved (global property).

Why do we care if a distributed system is linearizable? Systems that behave in a linearizable fashion are easy to reason about by programmers. This makes programming it much easier! Imagine writing a program using a key-value store that returns the latest value when we access on one node but an earlier value on another node. There would be a lot more programming effort on the client side to make sure the user program behaves consistently.

So lets step back a bit and talk about what the above 2 constraints mean. The first one is pretty easy and is what one would expect: operations that are executed on a single machine preserves the program order of operations. In fact I do not know any consistency models that does not satisfy this. The second requirement takes some explanation. Consider the following 2 operations Put and Get executed by servers S0 and S1 on a shared key-value store:

```
S0: Put(x=1)----------Put_OK
S1:                            Get(x)----------Get_OK(x=1)
```

Assuming our key x here starts with a value 0. Time goes from left to right and we think of the operation occurs at some instant between its invocation and completion (but we don't know exactly when). The above example is linearizable because completion of Put (e.g. Put_OK) occurs before invocation of Get (e.g. Get(x)), therefore we expect Get to return 1 and not 0. Lets see another example:

```
S0: Put(x=1)----------Put_OK
S1:         Get(x)------------Get_OK(x=0)
```

Is this linearizable? See that now there is a period between the 2 operations where they overlap (i.e. ran concurrently). The result of Get could be different depending on which operation took place first. This system may or may not be linearizable because too little information about the completion-to-invocation order is given. In order to make a definitive conclusion about this system about its linearizability, we need to examine more operations. Lets see an example that is not linearizable:

```
S0: Put(x=1)----------Put_OK
S1:         Get(x)------------Get_OK(x=0)  Get(x)------------Get_OK(x=0)
```

The second Get on S1 was invoked after Put on S0 completed. In a linearizable system we would expect that the second Get returns 1.

## Consistency Models

The term linearizability describes a consistency model. A consistency model is essentially a contract between a system with shared memory and the software that uses it. In the previous examples, we have explicitly indicated what are the semantics of our key-value store given the ordering of Puts and Gets. Without knowing the consistency model of a shared memory system is like given a tool without the manual that comes with it. How does the key-value store behave when we have concurrent threads reading/writing the same key? The first question to ask when you want to dig deep into a distributed system is: what is its consistency model?

Linearizability is the strictest consistency model that is achievable by any distributed systems today. How "Strict" here correlates to how much the system behaves like a multiprocessor shared memory model where everything occurs instantaneously. However, most productions systems actually aren't linearizable for the sake of efficiency. I won't go into more detail about why this is so, but it is easy to see that designing a distributed system where nodes far apart connected by unreliable network behaves like processors connected by memory busses is **hard**.

Historically there has been systems built with consistency models that are not linearizable. For example, [Bayou][bayou] and [COPS][cops] describes more relaxed consistency models where changes in one node is eventually propagated to other nodes in the system. More recent industry systems like Amazon's [Dynamo][dynamo] and Yahoo's [PNUTS][pnuts] showed that eventually consistent systems can scale when used in massive applications with sacrifice of speed and some caveats.

## Paxos and Linearizability

So why all this fuss about linearizability when we're talking about Paxos, a consensus algorithm? The implementation I am about to provide in the next part is a linearizable key-value store that uses Paxos to achieve consistency. Hopefully this provides the foundation for understanding what the expected behavior is and how we can use Paxos to achieve it. Stay tuned for part 2!

[leslie]: http://news.cs.nyu.edu/~jinyang/fa08/papers/paxos-simple.pdf
[bayou]: http://news.cs.nyu.edu/~jinyang/fa08/papers/bayou.pdf
[cops]: http://www.cs.princeton.edu/~mfreed/docs/cops-sosp11.pdf
[dynamo]: http://news.cs.nyu.edu/~jinyang/fa15-ds/papers/dynamo.pdf
[pnuts]: http://news.cs.nyu.edu/~jinyang/fa15-ds/papers/cooper-pnuts.pdf

