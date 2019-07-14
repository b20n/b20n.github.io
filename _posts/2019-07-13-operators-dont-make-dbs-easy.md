---
layout: post
title:  "Operators Don't Make Databases Easy"
date: 2019-07-13
---

A Kubernetes Operator is two things:

1. A Kubernetes-native interface (this is under-exploited!)
2. A set of patterns for writing level-triggered distributed systems

What part of that makes running a database easy?

Installing a database is not the hard problem. Rarely, if ever, is a
distributed system simplified by muxing in another distributed system.
At a minimum it's guaranteed to expand the state space.

This isn't to say that Operators aren't useful or interesting - they
are - but they're neither necessary nor sufficient for making
management of distributed stateful systems - e.g., databases - easy.
