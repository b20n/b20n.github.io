---
layout: post
title:  "Small Operators"
date: 2019-04-26
---

> I assumed that the controller in the operator model is generally more
> intelligent than a simple REST server...

As is the norm with software marketing campaigns, the advocates of Kubernetes
operators have generally emphasized the world-changing, paradigm-shifting nature
of the pattern. While there's no fault in that pragmatic strategy - it's been
effective - it seems to cause developers to overlook the benefit of operators
for "small" problems.

Setting aside grand notions of anomaly detection, self-healing, and even
control loops, the operator pattern is useful for its interface alone:

- The interface is declarative, and declarative interfaces in to stateful
  systems are natural fits. Anyone doing serious work on Kubernetes
  already understands this approach.

- Operators use the same interface language - Kubernetes resources - as the
  underlying compute platform, making both consumption (e.g., via Helm) and
  composition (e.g., layering operators) trivial.

- Kubernetes provides useful abstractions such as RBAC, garbage collection, and
  an "admin interface" - i.e., `kubectl` for free.

Once an organization is over the initial hurdle of designing and deploying
operators, operator-shaped solutions to relatively simple problems start to look
appealing. For example: writing an operator interface for creating object
storage buckets is a trivial exercise, but the result is a tool that (a) can be
reused across projects, (b) integrates seamlessly with other deployment pipeline
components, and (c) extends declarative dependency management beyond native
Kubernetes resources.

_Operators don't have to be big to be useful._
