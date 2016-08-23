---
layout: post
title:  "Lenses for Monitoring"
date: 2015-09-28
---

Designing monitoring and alerting systems for cloud services is surprisingly
challenging. It's a subtle problem space, and the obvious choices are almost
certainly poor ones. When I'm working with such problems I often find it
useful to construct mental frameworks, or lenses, to guide my decision making.
These take time and effort to develop and they need regular care and feeding,
but they're invaluable for making clear, consistent, well-reasoned decisions as
individuals and as teams.

This piece is a snapshot of my "monitoring and alerting" lenses, taken for
mental clarification and to aid communication.

# Monitoring

Developing monitoring for a new system or revamping monitoring for an old one
can certainly be a daunting task[^1]. I regularly see smart people get stuck on
"what do I monitor" even in situations where they are familiar with the codebase
in question.

The approach I take is to try to be very clear about the aims of my monitoring
system by grouping monitors in three categories:

* Failure detection, or "how do I know that something is wrong". This category
  covers monitors at the boundaries of your system. Your customer interactions,
  for example, are first and foremost here[^2], but any intra- or inter-service
  communications should be a priority as well.
* Failure investigation, i.e., "how do I find out what's broken". This is
  usually a pretty big and ever-expanding bucket, but looking at resource
  constraints[^3] is usually a good guiding principle. Don't hesitate to add
  more than you think you need.
* Long-term trending. Capacity planning is a pretty hard problem. I'm usually
  shooting in the dark long after my systems are in production, but it all has
  to start with data collection.

Because these categories are all end-result focused it's relatively easy for me
to assess the current state of my system and identify gaps that need addressing.
It's also useful to have a shared understanding of the various aims of your
monitoring systems among your team members[^4].

# Alerting

Designing alerts is a harder problem than defining new metrics to collect. Any
decent telemetry database doesn't care how many datapoints you throw at it, but
you're going to struggle to operate your systems effectively if you crush your
teammates with alert fatigue.

It's very important, I think, so always keep in mind long-term planning and
prioritization when assessing alerting for a given system. A healthy alerting
toolkit can act as a powerful feedback mechanism for your development process.
If it's not well-managed, or that feedback is ignored, your system will be
inoperable.

To that end I've found it's very effective to split alerts in two categories -
bridges to automation, and failure alerts - and treat them somewhat
independently.

#### Failure alerting

Alerts on user-visible failures should be the core of your suite. If a
customer's interactions with your system are failing, your operators should to
know about it. When starting from a blank slate, these alerts should be easy to
add, since they generally require little understanding of the underlying system.

In any system of nontrivial complexity the cause of "elevated latencies" is
almost certainly unknowable at a glance. Your monitoring systems should be
alerting you to those elevated latencies (_especially_ if they are nearing SLA
violations[^7]) and those alerts should link the operator to some sort of
document to help them investigate the issue.

Writing this kind of "broad investigation" document is not easy, and I've found
it difficult to learn and teach good investigation techniques. But every page
should come with instructions, even if those instructions are "Good luck".

#### Bridges to automation

Over the lifetime of a system - developing new features, gaining new customers -
it's natural to find problems "in the wild" that require alerts. Often you'll
find them through failure alerting, but once you've identified an underlying
problem you don't want it to affect customers again, right?

Since the problems are never-before-seen (by definition) you may not be
completely comfortable with your resolution procedure, or perhaps you don't have
the tooling in place to automate the task[^5], so you leave it to a human to
babysit. These alerts are _bridges_ to a future automated task or bugfixes.

The key here is that these bridges are temporary. Without a commitment to
development time toward the necessary automation or bugfixes you'll quickly find
yourself consumed by alerts that are trivial in isolation but overwhelming in
concert.

# Wrap

I'm not writing this to convince anyone that I'm right; I called this piece a
"snapshot" deliberately. As I've gained experience with these problems my point
of view and opinions have changed, and I expect they'll continue to do so. As of
this writing and as well as I can explain them in words to an anonymous
audience, this is their current state. The point here is not that there's a
right answer, but instead that it's valuable to think about your thinking, to
carefully examine your decisions, and to communicate the perspective motivating
those decisions. There aren't _too many_ bad opinions to be had here.

Thanks to Mike Wallace, Russell Branca, Ben Bastian, and Ulises Cervino for
reading and commenting on drafts of this post.

[^1]: I'm assuming here a base level of monitoring understanding and tooling -
    e.g., that collecting hundreds or thousands of datapoints per second is not
    technically infeasible, the programmer is aware of the need to collect
    accurate statistics (percentiles, not averages, etc.) and so on. That is,
    I'm interested in answering the "what", not the "how".

[^2]: You do have an SLA on 9xth percentile latencies, right?

[^3]: Obligatory [USE Method](http://www.brendangregg.com/usemethod.html) link.

[^4]: Hence, of course, this post.

[^5]: The complexity of said automation tooling may trend to "strong AI" as the
    complexity of the underlying problems increases, but that's okay - the point
    is the awareness and long-term commitment to improvement. There's not enough
    time to automate everything.

[^6]: Or fixing the problem at the "root cause".

[^7]: You do have an SLA on 9xth percentile latencies, right?
