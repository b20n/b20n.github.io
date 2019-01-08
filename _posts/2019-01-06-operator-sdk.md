---
layout: post
title:  "Whither Operator SDK?"
date: 2019-01-06
---

_tl;dr: the Operator SDK is probably not the framework you're looking for_

Back in mid-2018, the folks at CoreOS announced [with some fanfare][1] the "Operator
Framework":

> To help make it easier to build Kubernetes applications [...] an open source
> toolkit designed to manage Kubernetes native applications, called Operators,
> in a more effective, automated, and scalable way

The framework is composed of three components:

- Operator SDK
- Operator Lifecycle Manager
- Operator Metering

The SDK is described as follows:

> Enables developers to build Operators based on their expertise without
> requiring knowledge of Kubernetes API complexities.

As the most obviously useful component, the SDK garnered the most attention from
the community - the Lifecycle Manager and Metering components have bootstrapping
problems, and aren't terribly interesting if you're not running operators
already.

A programmer new to operators in 2019 might be surprised to find, then, that the
"Operator SDK" isn't much of an "SDK" at all - for Go operators, at least, it
doesn't appear do much beyond templating out some [Kubebuilder][2]-based code.

Standardization around Kubebuilder is probably a great thing for the community -
it's being tracked by the API Machinery SIG, after all - but it's not obvious
that there's much to be gained by using the Operator SDK rather than Kubebuilder
directly. If you're writing basic Go operators, check out Kubebuilder, not the
Operator SDK.

P.S.: This isn't intended to be critical of the Operator Framework as a whole.
The Lifecycle Manager and Metering projects seem like great ideas, and the SDK
includes some nascent support for Helm- and Ansible- based operators that I
haven't spent any time with. I'm talking strictly about writing resources and
controllers directly in Go.

P.P.S: In case you're curious - I was - [etcd-operator][3]
and [prometheus-operator][4] are implemented with basic `client-go` based
controllers. No Operator SDK, no Kubebuilder, etc.

P.P.P.S: If you're going to be doing serious work writing resources and
controllers, it would behoove you to get familiar with those basic `client-go`
based controllers. That's how the controllers in `controller-manager` are
implemented, and those are your best source of guidance.

[1]: https://coreos.com/blog/introducing-operator-framework
[2]: http://kubebuilder.netlify.com/
[3]: https://github.com/coreos/etcd-operator
[4]: https://github.com/coreos/prometheus-operator
