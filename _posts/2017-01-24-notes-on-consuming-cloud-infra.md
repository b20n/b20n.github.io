---
layout: post
title:  "Notes on the Healthy Consumption of Cloud Infrastructure Services"
date: 2017-01-24
---

Cloud infrastructure (e.g., DBaaS) is all the rage these days. Fun times. Over
the past five years I've spent building and operating cloud infrastructure, I've
learned that it's not always obvious how to be an effective _consumer_ of these
services. Relationships often suffer during incidents due to a mismatch between
the service provider's level of communication and the customer's. This is
understandable - after all, as a DBaaS _customer_ your DBaaS is a cost of doing
business, not your business - but it's absolutely in your best interest to be a
"good" customer - you'll get more support attention, you'll be in a better
position to evaluate providers, and ultimately you'll probably build more
resilient software.

Put another way: viewed through a risk management lens, outsourcing
infrastructure exposes you to significant operational risks; learning how to
effectively work with a service provider is a way to minimize those risks.

These notes are in no particular order. Some are more important than others, and
I've certainly left out something important. Et cetera.

Most of these have corollaries as well - does your service provider not have an
SLA? You should probably find someone else. Does your service provider not
measure latency? Run away!

#### Measure latency and do statistics

Assuming you're working in a transactional world, you should measure the latency
of your system's interactions with the provider's system. Latency and
connectivity are the boundaries across which your contract is fulfilled, and
probably the most important aspect of your interactions with the system.

If you have several request classes, measure the latency of each one.
Specificity is useful for the provider's debugging.

Don't trust the provider to measure latency for you. Some router in the middle
will make you regret it.

Perform meaningful statistics on the latency measurements you capture. Don't
report mean latency; percentiles - especially tails - are much more instructive.
Don't take the mean of two percentiles.

[This][1] is a great talk on latency in distributed systems.

#### Have a timeout

Your provider's system is probably going to stop responding at some point. Make
sure your requests have a meaningful (i.e., socket-level) timeout. When that
timeout is hit, do something sensible. If you need to retry, apply exponential
backoff and jitter to avoid compounding the problem.

#### Leverage ways to communicate intent

Take the time to understand the provider's API. If there are ways to communicate
the intent of your request, use them. For example, if you can specify a timeout
value along with a request, that can be a tremendous aid to the service provider
in shedding load in overload scenarios.

#### Don't blindly consume

Understand the semantics of the system you're consuming. If you're working with
CouchDB, for example, don't expect to update your map/reduce views every day. If
you're working with Cassandra, don't expect consistency. If it's not clear, ask:
your service provider would probably love feedback on their documentation.

#### Understand SLAs

An SLA is not a guarantee. It's a contract. "99.999% uptime" does not mean that
the provider's system will be available 99.999% percent of the time; it means
that if it isn't, you (typically) are entitled to remediation. Understand how
the provider's SLA is measured. More importantly, understand what your
application is going to do in that 0.001% of the time that the provider isn't
available.

#### Manage your humans

A reputation as a "difficult" customer is not an easy thing to shake. On the
flip side, an internal culture of blaming the service provider is even harder.
Be courteous.

[1]: https://www.youtube.com/watch?v=C_PxVdQmfpk
