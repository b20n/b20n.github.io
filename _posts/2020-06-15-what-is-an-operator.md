---
layout: post
title:  "What is an Operator, Anyway?"
date: 2020-06-15
---

The modern consumer expects Internet services that are always-on,
fast, and cheap. These expectations place a burden on the shoulders of
implementors and operators of these services, to deliver them in an
economical fashion. Over the past twenty years there has been an
explosion of tools, both technical and nontechnical, designed to meet
this challenge. DevOps, Site Reliability Engineering,
infrastructure-as-code, cattle-not-pets, containerization, NoSQL,
Kubernetes, serverless - all of these have played a part in the
evolution of the state of the art. To some extent the future is here
(Kubernetes would be near-unthinkable by the average engineer in 2000,
but is commonplace in 2020) but it's not evenly distributed.

Many of these tools focus to near-exclusivity on the problems of
stateless services, such as API servers and other so-called
"microservices". This makes some sense; stateless services are
relatively simple from an run-time point of view. But it's a rare
stateless service that exists without the backing of a stateful
system, such as a database or a message queue. And stateful systems
are another beast entirely.

Managing stateful systems effectively and economically - scaling,
upgrading, reconfiguring, backing up, recoving from failure - requires
careful application of domain knowledge that cannot be encoded in
Kubernetes. This isn't to say that it's not possible to deploy
stateful systems to Kubernetes, or that "operable" stateful systems
don't exist. But it is the case that as a community of practice we do
not have a shared understanding of how to do this in a repeatable,
reliable, and economical fashion. The closest things we have come from
the observability space, such as runbooks and alerts, and they don't
scale.

Operators are an answer to this problem. They are a tool for
delivering and managing stateful systems running on Kubernetes. An
operator is higher-level software that implements the behaviors to
manage a stateful system in a robust manner on top of the Kubernetes
runtime.

The heart of an operator is a controller. The controller periodically
inspects "the world" and compares to a representation of the desired
state of that world. If any action is necessary to make the world more
closely reflect the desired state, the controller takes that action.
This loop is performed in perpetuity. Think of a thermostat: every so
often, it observes the state of "the world" - in this case, the
ambient temperature - and compares it to the user's desired state of
that world - say, seventy degrees Farenheit. If the world is too cold,
the furnace is powered on. If the world is too hot, perhaps the
furnace will be powered off. Some time later, the thermostat will
inspect the state of the world again, compare to the user's desired
state (perhaps seventy-one degrees this time!) and again take
necessary action.

Despite the apparent triviality of the thermostat example it turns out
that this pattern can be effectively applied to solve a broad range of
systems management problems. This is not an accident; the
fundamentals of controllers have been well-explored and systematized
in the field of control theory which dates back to Maxwell in the
nineteenth century. There's a big difference between an operator that
provisions an object storage bucket and a PID controller that
auto-tunes a pool of web servers, but the basic idea is pretty
similar, once you've got a control theory lens on the world.

In the context of of Kubernetes, a minimal operator comprises two
components: a special type of resource called a
`CustomResourceDefinition`, or `CRD` and a controller that uses
instances of that `CRD` as the specification of the desired state of
the world.

An instance of a `CRD` is a Kubernetes resource like any other. It
includes standard `ObjectMeta` fields such as `Name`,
`CreationTimestamp`, `ResourceVersion`, and `Generation`, as well as
`Spec` and `Status` fields whose underlying structure is in the hands
of the implementor. For example, the manifest below defines a custom
resource called "`Foo` " in the `programmingoperators.com/v1` namespace:

```yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: foos.programmingoperators.com
spec:
  group: programmingoperators.com
  names:
    plural: foos
    singular: foo
    kind: Foo
  versions:
    - name: v1
      served: true
      storage: true
      subresources:
        # status enables the status subresource.
        status: {}
```

And here's a sample manifest of an instance of that `CRD`:

```yaml
apiVersion: "programmingoperators.com/v1"
kind: Foo
metadata:
  name: my-foo
spec:
  [ ... ]
status:
  [ ... ]
```

A controller associated with this resource uses the Kubernetes APIs to
watch instances of `foos.programmingoperators.com/v1` and take action
against "the world" when necessary. It might create or mutate other
Kubernetes resources, reconfigure a database, or launch missiles;
beyond some basic limitations, Kubernetes doesn't much care.

That's it, really. "Operator" may imply big ideas about autonomous,
self-healing distributed systems and thousands of SREs on the streets
looking for jobs, but it doesn't entail them. An operator can be as
big or as small as the role it needs to fill with a system. To dip in
just a bit deeper let's touch on two big ideas.

The first is related to the fact that Operators exist at the behest of
the Kubernetes API, and that API has very particular semantics. One of
these semantics, and perhaps the most important for operator
programmers, is that there is no way to observe every change to a
resource. Whatever your operator needs to do, it neds to do based on
the current state of the resource at any given time. This idea is
sometimes called "level-triggering", and it's essential to writing
operators that do what they say on the box.

The second big idea is convergence. Given a resource with a certain
specification, the controller should eventually converge to a stable
state where no more action is taken. "Eventually" could be a very long
time - hundreds of iterations, or hours in the real-world - but the
controller should be working to align the state of the world with the
state specified in the resources it manages. If that specified state
changes - even before the world has been converged - the controller
should start moving towards the new state.
