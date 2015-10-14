---
layout: post
title:  "Lenses for Monitoring"
date:   2015-09-28 21:12:58
---

When I'm working on complex problems I often find it useful to construct mental
frameworks, or lenses, to guide my decision making. These take time and effort
to develop and they need regular care and feeding, but they're invaluable for
making clear, consistent, well-reasoned decisions as individuals and as teams.

What follows is a snapshot of my monitoring lenses, taken to aid clarification
and communication. I've spent a fair bit of time working on and thinking about
monitoring systems so my opinions are somewhat strong and perhaps idiosyncratic,
but I like to believe they're weakly held.

# Monitoring

Developing monitoring for a new system or revamping monitoring for an old one
can certainly be a daunting task[^1]. I regularly see smart people get stuck on
"what do I monitor" even in situations where he or she is familiar with the
codebase in question.

The approach I take is to try to be very clear about the aims of my monitoring
system by grouping monitors in three categories:

* Failure detection, or "how do I know that something is wrong". This category
  covers monitors at the boundaries of your system. Your customer interactions,
  for example, are first and foremost here[^2], but any intra- or inter-service
  communcations should be a priority as well.
* Failure investigation, i.e., "how do I find out what's broken". This is
  usually a pretty big and ever-expanding bucket, but looking at resource
  constraints[^3] is usually a good guiding principle. Don't hesitate to add
  more than you think you need.
* Long-term trending. If you're any good at capacity planning, good for you. I'm
  usually shooting in the dark on these for a long while after my systems are in
  production.

Because these categories are all end-result focused it's relatively easy for me
to assess the current state of my system and identify gaps that need addressing.
It's also useful to have a shared understanding of the various aims of your
monitoring systems among your team members[^4].

# Alerting

Designing alerts is a rather harder problem than defining new metrics to
collect. Any decent telemetry database doesn't care how many datapoints you
throw at it, but you're going to struggle to operate your systems effectively if
you crush your teammates with alert fatigue. So it pays to be careful. Again,
though, using an end-result focused viewpoint is effective.

#### Bridges to automation, or, "on playbooks"

I don't much like the term "playbook[^5]", because it tends to make people think
that every alert needs to have a set of specific actionable steps to resolution.
I don't think that's true (see below). It also makes people wonder why they're
even involved - if the alert has a deterministic or near-deterministic
resolution procedure, why is the human being alerted?

I find it's useful to think of deterministic or near-deterministic playbooks as
"bridges" toward automated processes. Perhaps you're not completely comfortable
with the procedure yet, or you don't have the tooling in place to automate this
particular task, so it's left to a human. These things are going to happen to
every team at some point, but if you look at the alert/playbook as a _bridge_ to
a future automated task, and make a commitment to provide dedicated development
time toward that automation[^6], you'll be on a good path.

#### Failure alerting

Not every alert has a direct path to action. In any system of nontrivial
complexity the cause of "elevated latencies" is almost certainly unknowable at a
glance. Your monitoring systems should be alerting you to those elevated
latencies (_especially_ if they are nearing SLA violations[^7]) and those alerts
should link the operator to some sort of document, which may or may not be
called a "playbook", to help them investigate the issue.

Writing this kind of "broad investigation" document is not easy, and I've found
that it's exceedingly difficult to teach good investigation techniques. But
every page should come with instructions, even if those instructions are "Good
luck", and the lack of a defined procedure cannot block you from adding alerts.

# Wrap

These lenses work well for me. They might not work well for you. In any case
it's valuable to think about your thinking, and examining your decision making
processes in a complex space such as monitoring and alerting is a good place for
it. No matter what ideas you come up with, if they're well communicated amongst
your co-operators you'll all be better off for it.

[^1]: I'm assuming here a base level of monitoring understanding and tooling -
    e.g., that collecting hundreds or thousands of datapoints per second is not
    technically infeasible, the programmer is aware of the need to collect
    accurate statistics (percentiles, not averages, etc.) and so on. That is,
    I'm interested in answering the "what", not the "how".

[^2]: You do have an SLA on 9xth percentile latencies, right?

[^3]: Obligatory [USE Method](http://www.brendangregg.com/usemethod.html) link.

[^4]: Hence, of course, this post.

[^5]: Or "runbook".

[^6]: Or, of course, fixing the problem at the root of the reason for the alert.

[^7]: You do have an SLA on 9xth percentile latencies, right?
