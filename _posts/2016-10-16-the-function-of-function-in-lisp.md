---
layout: post
title: "Paper: The Function of FUNCTION in LISP"
date: 2016-10-16
---

[Joel Moses, AI Memo 199, June 1970][1]

> A problem comon to many powerful programming languages arises when one has to
> determine what values to assign to free variables in functions.

Lambda abstractions ("first class functions", "anonymous functions", "function
literals") have entered the average programmer's lexicon through the growing
popularity of languages that support them and their inclusion in the
"mainstream" (read: Java 8).

A similar statement could be made for "closures", despite the fact that
anonymous functions and closures are _not the same thing_, something that
becomes abundantly clear as soon as one attempts to implement them in a
programming language.

To be clear, this is not a closure:

```
(def foo (lambda (x) (+ 1 x)))
```

but this is:

```
(def bar 4)
(def baz (lambda (x) (+ bar x)))
```

The second example _closes over_ the "free" reference to `bar`. Depending on the
semantics of the language in question, this has some interesting consequences.
If bindings are mutable, then the value of `bar` may change at runtime, thereby
changing the behavior of `baz`.

If you've spent any time reading old MIT AI Memos from the 1960's and 1970s,
you've undoubtedly seen references to the "FUNARG problem". This issue -
implementing what we now call "closures" (after [Peter Landin][2]) - is the
essence of the FUNARG problem, and the focus of Moses' paper.

Of course, Moses would likely argue with my terminology here: this paper is
subtitled, _Why the FUNARG Problem Should be Called the Environment Problem_.

The paper begins with a description of common language implementation techniques
(using stacks to maintain variable references) and points out the clear
problem - it's not obvious how to support references to free/closed-over
variables in a stack-based approach to program execution. Moses goes on to
discuss tradeoffs between available solutions to the problem, introducing the
"environment" concept in the process, and how they may impact the semantics of
users' programs.

It's important to view this paper in its proper historical context. LISP was
little over ten years old at this point. Lexical scope was in use in ALGOL, but
did not exist in the modern sense in LISP (and LISP 1.5 was dynamically scoped).
Lazy/call-by-need evaluation had not yet been explored. Immutable
data/references were not in wide use. These concepts all have significant impact
on the ideas expressed in this paper. There is a wealth of information available
on implementing closures in programming languages; don't take this paper as the
end-all truth.

> The use of free variables is a very powerful computational idea. [...] it may
> be possible to avoid using free variables by supplying a function with
> additional arguments. This can be quite cumbersome. It is also inefficient
> since a subroutine call might require the saving of dozens of parameters.

Here's an obvious approach to solving the problem - replace a function's
references to free variables with arguments. This approach is referred to
as ["lambda lifting"][3], and is relatively easily discoverable by anyone
implementing their own toy language. Moses dismisses the approach for reasons
that are perhaps understandable.

> The idea of substituting the values of the free variables inside the functions
> is also very inefficient [...]

It is possible that Moses underestimates the level of complexity compiler
writers would be willing to accept 30 years in the future.

> both of these methods do not allow one to modify the value of the free
> variable and achieve the desired effect.

This is very important, and historically interesting. Remember, there's no
immutability here, and little concern for the potential for unbounded use of
this technique to hamper program clarity.

> When a function uses free variables or when it calls functions which user
> them, then the value of this function is not completely determined by its
> arguments, since the value might depend on the values of the free variables.
> Thus in order to completely specify a computation, one has to specify a
> function and its arguments in addition to an _environment_ in which the values
> of the free variables can be determined.

Straightforward stuff. Moses goes on to discuss an important distinction between
different environments: the "binding environment" and "activation environment",
and suggests this as a difficult problem, particularly because traditional
techniques would call for discarding stack segments that would in fact be later
needed as bindings at activation time. This ultimately generalizes to the notion
of an environment "tree".

[1]: https://dspace.mit.edu/bitstream/handle/1721.1/5854/AIM-199.pdf
[2]: https://scholar.google.com/scholar?cluster=2086590721921694165
[3]: https://en.wikipedia.org/wiki/Lambda_lifting
