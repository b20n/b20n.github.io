---
layout: post
title:  "Coding Conformity"
date: 2016-11-02
---

(or, [WTFs/m][1], but wordier)

Underappreciating the value of conformity to existing conventions and idioms
seems to be a common programmer mistake. This applies at various levels:

- Conventions within a given project - line length, indentation, or identifier
  casing

- Conventions within a given programming language - iteration vs. recursion,
  or classes vs. hashes

While it's tempting to view programming through a lens of "self-expression" and
treat guidelines like these as stamps of authoritarianism, that turns out to be
a real trap when working on real projects with other developers[^1].

The programming "discipline" is _terrible_ at comprehension; I'd be willing to
argue that program comprehension is the single most important challenge we
programmers have in our field. This turns up in myriad places, from people with
much more credibility than me:

- Knuth's (!) experiments in literate programming

- Abelson: "Programs must be written for people to read, and only incidentally
  for machines to execute."

- Dijkstra: "[...] creating confidence in the correctness of his design was the
  most important but hardest aspect of the programmer's task [...]"

Recognizing that you need all the help you can get in program comprehension is a
mark of a mature programmer[^2]. Recognizing that conventions are there for
_exactly that reason_ is another. Conformity to a consistent set of conventions
and idioms aids program comprehension by reducing the cognitive burden on
programmers interacting with that program.

I don't read code character-by-character, or line-by-line. I spend most of my
time looking and and thinking about higher-level structures. If the code I'm
reading is nonidiomatic with regard to a programming language I'm familiar with,
or various classes and modules don't conform to one another's conventions, then
my ability to comprehend it - and thereby work with effectively - drops
precipitously.

Incidentally, this is related to the idea of "programming as self-expression" -
I don't know many good programmers that think indentation styles are an
interesting mode of expression. The interesting parts of "programming as
self-expression" happen at a higher level - abstractions, modularity,
communication. Again, a lack of conformity just gets in the way.

[1]: http://www.osnews.com/story/19266/WTFs_m

[^1]: One of whom may be you, a year from now.

[^2]: A secondary point: it's not good enough to be able to conform to a
      specification (though in some situations a specification may be necessary)
      - most conventions are implicit, and you're never going to understand them
      without reading code.
