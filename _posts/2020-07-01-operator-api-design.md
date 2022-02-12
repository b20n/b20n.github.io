---
layout: post
title:  "Notes on Operator API Design"
date: 2020-07-01
---

As is common with much of computing, user experience is an
oft-neglected part of building effective operators. Controller
behavior really is the heart of the pattern, but it must not be
forgotten that the `CustomResourceDefinitions` exposed by an operator
are an interface. They're the entrypoint for humans and computers in
to your system; if they're a mess, no one is going to be eager to use
it. This goes for both inputs - the spec - and outputs - the status -
of your `CustomResourceDefinitions`. 

### Fat CRDs, God Objects, and RBAC

When surveying the state of the art in open-source operators it's
common to see a one-to-one correspondence between an operator and a
`CustomResourceDefinition`. Assuming these operators are all doing
something useful, this is unfortunate for a couple of reasons.

First, off, analogous to the "god object" problem in object-oriented
programming, "fat" CRDs provide interfaces (and require
implementations!) that unecessarily couple systems across unrelated
concerns. This can make programming against your operator difficult;
"everything knows about everything" and its associated brittleness
will leak in to your users' applications.

Consider, for example, an operator for managing a PostgreSQL database
that supports both provisioning the database and managing its backups.
One way to express this would be to use a `PostgreSQLInstance` CRD
with a `Backups` sub-field inside `Status`:

```yaml
apiVersion: "api.programmingoperators.com/v1"
kind: PostgreSQLInstance
metadata:
  name: my-psql
spec:
  replicas: 2
status:
  backups:
  - 1581742748
  - 1581656378
```

This will work, more or less, but it's definitely not ideal. For one,
it leaves the user with no interface to work with an individual
backup, e.g., to delete it. A more useful structure would abstract out
a `PostgreSQLBackup` CRD and support operations directly against it,
instead:

```yaml
apiVersion: "api.programmingoperators.com/v1"
kind: PostgreSQLBackup
metadata:
  name: my-first-backup
spec:
  expires: 1584161978
status:
  state: completed
```

The operator can then expose a declarative interface for the backups -
creating a `PostgreSQLBackup` causes a backup to be taken with the
given specification, deleting one causes it to be deleted, etc.

In general, looking to object-oriented programming patterns as a model
for CRD design isn't a bad idea, though keep in mind they're not
free - the single responsibility principle might be taking things a
bit too far.

Carrying on a bit deeper with this PostgreSQL example, let's talk
about RBAC. Kubernetes' role-based access control system is a
tremendously important part of operators. In what other system can you
get a fully-baked authz model for your distributed system, with all
the bells, whistles, and integrations you could want, for free, out of
the box? It is critical not to overlook RBAC when designing CRDs.

In the first example above, where backups are embedded in the
`PostgreSQLInstance` CRD, it's impossible for a user to write a tool
to delete a backup that cannot also delete the entire PostgreSQL
instance, perhaps with all of its data! On the other hand, with the
dedicated `PostgreSQLBackup` CRD, users can define fine-grained RBAC
rules that permit manipulation of backups independent of permissions
on instances.

Users need to implement services with minimal responsibilities, and
the tools to enable them are right in front of you. Use them!

### Use built-in types liberally

Kubernetes is a pretty "big" system. It covers quite a lot of
conceptual ground in the data represented by its basic functionality,
from abstract concepts like `Deployment` and `Pod` to more concrete
ones such as bytes of memory. When designing your CRD specifications,
you can achieve a much more "native" look-and-feel if you adopt types
from the upstream libraries directly.

For example, if you're representing a resource, such as memory, don't
ever do this:

```go
type FooSpec struct {
    // How much memory to allocate to a Foo
    MemoryInBytes int32
}
```

Instead, use a `resource.Quantity`, just like Kubernetes resources do:

```go
type FooSpec struct {
    // How much memory to allocate to a Foo
    Memory resource.Quantity
}
```

This approach allows both you and your users to take advantage of the
work the Kubernetes authors have already done - parsing human-readable
strings, efficient conversions under the hood, etc. And your users are
already familiar with it - minimize cognitive burden!

### Make generations meaningful

From an API consumer's point of view, one of the more challenging
poarts of working with operators is understanding when a specific
action is completed. This flows naturally from the declarative nature
of Kubernetes: a successful response to an API call represents only
the acceptance of a request - not its execution. For all the caller
knows, that request may not be possible to satisfy, or the relevant
controller may be out to lunch, or it may take a week to complete by
design - it's not immediately obvious. This is a general problem for
Kubernetes controllers. For example, try figuring out when an image
change on a Deployment is completely rolled-out - it's not
straightforward. Many controllers don't even bother to communicate
convergence progress; those that do generally do is in an ad-hoc
manner. It is unfortunately up to the user to understand the semantics
of each resource-controller pair.

As the designer of CRD-based operators this is a problem for you, too.
And of course, there's no "right" answer - if there were, it'd be
standardized. One thing that you can do, however, is to use resource
generations. It's quite simple, and you should probably be using them
anyway.

Resource generations are part of the `ObjectMeta` struct embedded in
every Kubernetes resource. Every time a resource's specification - not
status, not labels, not annotations, but specifically the `Spec`
field - is mutated, the generation is atomically incremented by the
API server.

For historical reasons, this works slightly differently for custom
resources. By default, generations are never incremented for custom
resources. In order to enable them, the "status" subresource must be
enabled on the `CustomResourceDefinition`, like this:

```yaml
apiVersion: programmingoperators.com/v1
kind: CustomResourceDefinition
metadata:
  name: foo.programmingoperators.com
spec:
  group: programmingoperators.
  versions:
    - name: v1
      served: true
      storage: true
      subresources:
        # status enables the status subresource.
        status: {}
```

With the status resource enabled, writes to the custom resource -
barring `.metadata` and `.status` fields - will increment the
resource's `.metadata.generation` field.

Note: if you're working operators in the wild, and want to migrate
existing resources to the support the status subresource, it is
possible to mutate existing `CustomResourceDefinitions` to enable it.
Do so with care, however: doing so will irrevocably destroy all
existing data at `.status`.

With the status subresource enabled we need one more datum to tie the
story together here: `observedGeneration`. A resource's "observed
generation" - `.status.observedGeneration` typically represents an
indication from the resource's controller that the resource's
specification at that generation has been reflected in the state of
the world. That is, from a caller's point of view, after mutating a
resource and noting the returned resource's `.metadata.generation`,
one must simply wait for `.metadata.observedGeneration >=
.metadata.generation`. At this point, the caller's request has been
"satisfied" in some sense.

Do note that this mechanism is ad-hoc; a controller's definition of
having "observed" a generation may be completely idiosyncratic. It's
up to you as the controller author to clearly document - and make
consistent - the semantics of `.metadata.observedGeneration` to help
your users make effective use of your operators.
