---
layout: post
title:  "Good Abstractions are Robust"
date: 2017-01-24
---

Abstraction is fundamental to programming - the lambda calculus, for example,
more or less _is_ abstraction - and its effective application is critical to the
sort of semantic (and typographic) compression that makes it possible to deal
with a computing system of any meaningful size. Not all abstractions are good,
however. Most are shoddy. A read through most software projects will show that
programmers' ability to design good abstractions is pretty hit-or-miss.

I'm not convinced that good abstraction design is something that can be taught;
at the very least our computer science departments don't seem to be doing a good
job. I'm not even convinced that our field has a clear understanding of what
makes a given abstraction good or bad. "I'll know it when I see it" is not much
of a pillar of a Science™, but it's the best that I'm aware of. But you can't
know it when you see it without spending time reading others' work, building
that muscle, and trying to understand the nature of good abstractions.

One useful abstraction-filter I've been using recently is the notion of
"robustness", in the [Gerry Sussman sense][1]:

> It is hard to build robust systems: systems that have acceptable behavior over
> a larger class of situations than was anticipated by their designers. The most
> robust systems are evolvable: they can be easily adapted to new situations
> with only minor modification.

It's easy to come up with good abstractions that fit this model - e.g., various
components of Unix, the lambda calculus, and the Von Neumann architecture all
exhibit this robustness property.

It's also instructive to read code and papers with an eye toward the robustness
of the abstractions involved. Usually authors won't mention it, or anything like
it, but it's often identifiable in retrospectives [like this one][2].

[1]: https://groups.csail.mit.edu/mac/users/gjs/6.945/readings/robust-systems.pdf
[2]: https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/acrobat-22.pdf
