---
layout: post
title:  "Paper: A History and Evaluation of System R"
date: 2016-06-01
---

[Chamberlin et al., CACM October 1981][1]

> System R, an experimental database system, was constructed to demonstrate that
> the usability advantages of the relational data model can be realized in a
> system with the complete function and high performance required for everyday
> production use. This paper describes the three principal phases of the System
> R project and discusses some of the lessons learned from System R about the
> design of relational systems and database systems in general.

While the relational model was proposed by E.F. Codd in 1970, as of the
publication of this article eleven years later its feasibility was still in
question:

> [...] the advantages of the relational model in terms of user productivity and
> data independence have become widely recognized. However, as in the early days
> of high-level programming languages, questions are sometimes raised about
> whether or not an automatic system can choose as efficient an algorithm for
> processing a complex query as a trained programmer would.

System R was a highly influential, _experimental_, database system designed by
IBM in order to demonstrate the feasibility of the relational model. Under
greatest scrutiny was the ability of an automatic system to translate queries
written in a high-level language to efficient programs for accessing data on
disk. Pre-relational systems did not decouple physical from logical data
relationships, forcing programmers to concretely specify access paths. By 1981
the benefits of this decoupling were "widely recognized" but not widely used;
the classic "hand-tuned versus automatic" debate (see: compilers versus
assemblers, malloc/free versus garbage collectors, etc.) was still in full
force.

Development of System R began in 1974 with a prototype of the SQL (then SEQUEL)
interface language. Two important things here from a software engineering
perspective:

1. This "Phase Zero" prototype was designed to be, and was, _thrown away_:

> One strongly felt conclusion was that it is a very good idea, in a project the
> size of System R, to plan to throw away the initial implementation.

2. The authors took humans seriously:

> [...] considerable thought was given to the human factors aspects [...] and an
> experimental study was conducted [...]

> Observation of some of the applications [...] convinced us of the importance
> of the "join formulation" of SQL.

Back on the database research end of things, the crux of the System R effort was
on what the research group calls "access path selection". Consider a query
requesting a list of all "employees that are programmers and who work in
Evanston". To satisfy this query the system may choose among several access
paths, for example:

- Performing a filtering scan of the "employees" relation
- Using an index on the "jobs" field to find programmers, then filter on location
- Using an index on the "location" field to find Evanston-based employees, then
  filter on job

Servicing the query via each of these access paths will incur a distinct cost in
terms of system resources (CPU, disk I/O, etc.); the purpose of the system is to
automatically choose "good" access paths, such that these costs are minimized.

Along with valuable user testing (e.g., SQL joins are useful!) the authors note
that "Phase Zero" was extremely useful in teaching lessons about query
optimization. These may seem obvious - of course the optimizer should take CPU
cost in to account along with I/O cost - but they're instructive examples of how
challenging it can be to predict the behavior of complex systems. It's not as if
the authors didn't understand databases; it's that they're complicated.

The discussions of Phase One and Phase Two get somewhat down in to the weeds,
but the paper remains an excellent read for both historical interest and
self-reflection.

[1]: https://scholar.google.com/scholar?cluster=1624408330930846885
