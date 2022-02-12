---
layout: post
title:  "A Brief, Incomplete, Early History of Managing Computer Systems"
date: 2020-07-22
---

N.B.: what follows is a rational reconstruction; it is not intended to
be a comprehensive history by any means. There are loads of great
resources out there on the history of computing and computers; this
isn't one of them.

### Beginnings: early electromechanical machines
The earliest days of modern computing are almost unrecognizable - no
stored programs, no programming languages, no displays, machines that
take up entire rooms - but the computers of the 1940s such as the
Harvard Mark I and the ENIAC are unmistakably computers in the modern
sense. The fact that they are so different from the world of the
twenty-first century provides a useful starting point for our
investigation. From a systems management perspective, several points
are worth noting:

- _Precision is important, too:_ In the context of the 1940s, one of
  the key features of these early machines was their calculation
  precision. The analog computers they replaced were precise only to
  two or three significant digits. The Harvard Mark I could calculate
  precisely to twenty-three digits. Unfortunately, unlike in modern
  systems where failures typically manifest as availability loss,
  component failure in an electromechanical computer could compromise
  precision as well, resulting in silent corruption of results.

- _Component failures really matter_: These machines had good reason
  to take up entire rooms. Without microchips, integrated circuits, or
  even transistors, they were composed of millions of discrete
  components, each subject to independent failure. There is an
  interesting distinction between the Harvard Mark I and the later
  ENIAC here. The Mark I engineers traded performance for reliability
  by using multipole relays in place of vacuum tubes, resulting in a
  machine that was able to operate nearly continuously for sixteen
  years. ENIAC, on the other hand, relied on nearly 18,000 vacuum
  tubes and rarely ran for more than a few days without blowing one
  out. It was a far faster machine, but only achieved approximately
  50% uptime.

- _Sysadmins matter, too_: These machines were extremely expensive,
  specialized pieces of hardware, usually dedicated to military
  purposes. Access to them was tightly controlled. ENIAC, for example,
  was run by a very small team that developed both the programming
  techniques as well as sophisticated operational procedures such as
  "don't turn it off because it'll blow tubes when it boots up".

Finally, no discussion of systems management on electromechanical
copmuters would be complete without mentioning the Semi-Automatic
Ground Environment, or SAGE. SAGE was a Cold War-era missile defense
system designed to coordinate and aggregate radar data across North
America. It composed hundreds of radar systems feedling data to a set
of twenty four "Direction Centers". Every Direction Center housed a
pair of AN/FSQ-7 computers each of which individually was the largest
discrete computer ever built: it weighed 250 tons, required a one
megawatt power supply, and housed 49,000 vacuum tubes. These machines
operated 24x7 in an active/standby pair delivering 99.95% uptime. Data
from radars across the continent was fed to the Direction Centers on
phone lines at 1300 bps. From a management perspective SAGE was a
landmark. Not only was it tackling harder problems by introducing
network failures and their attendant distributed systems problems, it
also set a higher bar in attempting to overcome them.

### Transistors and commercialization
While some commercial machines did exist in the vacuum tube era the
mass productionization of transistors in the early 1960s marked both
the beginning of a boom in commercial computing and a revolution in
systems reliability. With solid-state transistors replacing vacuum
tubes at the core of the machines, reliability increased tremedously.
The attendant revoluion in operational cost along with the diminished
physical scale and capital requirements of the machines made computing
available to a much wider audience than ever before. This led roughly
down two paths. One, which we'll return to later on, starts with the
TX-0 at MIT and proceeds through to the DEC PDPs and the Internet. The
other, which we'll trace here, remains in the realm of "big iron"
computing - impersonal business machines built along the lines of
their first-generation predecessors but taking advantage of
transistors' scale and reliability to achieve results far surpassing
the electromechanical systems.

These machines - for example, the IBM 7000 series and the CDC 6000
series - were the first "mainframes"; some variants were the first to
be labeled "supercomputers". Applications were still largely local and
batch-oriented, not interactive. Administration of the systems was
still a specialized task, solidifying the "high priesthood of
computing" trope. Reliability concerns were still typically directed
toward single-system uptime via component testing and local
redundancy. Reliability across multiple systems was usually achieved by
running identical workloads on active/active pairs of machines at
great cost. This line of thinking - "I've got this one system running
my application, make it as fast and reliable as possible" may seem
quaint to today's programmers steeped in commodity hardware approaches
to reliability, but in some circles it's still preached as high art -
IBM does, after all, continue to produce new Z-series mainframes year
after year.

#### SABRE
The overriding theme in second-generation/transistorized computing of
qualitative change, not quantitative change: new technology reduced
the cost of ownership of computers to the point that they became
economical for businesses, so the sorts of computing activities
performed for military and scientific purposes in the first generation
were adapted to business problems. This played out writ large with
SABRE, the "Semi-Automated Business Research Environment," whose name
should be reminiscent of the "Semi-Automatic Ground Environment" -
i.e., SAGE. (It seems that "SABE" didn't have the same ring to it).

The seat reservation system at American Airlines in the 1950s was
fundamentally unscalable. The entire process bottlenecked in a room
with eight humans standing around a lazy susan in Little Rock,
Arkansas, USA, and in the worst case it took upwards of three hours to
reserve a seat. With air traffic rapidly growing, this situation was
untenable. The obvious solution from IBM's perspective was to take the
principles of SAGE and apply them to American's problem.

SAGE was fundamentally a distributed system: a series of relatively
dumb terminals networked to a centralized store of radar data. Replace
radar readouts with reservation agents' terminals, and radar data with
a seat reservation database and you can quickly get the idea behind
SABRE. The first revision, deployed in 1960 at an unadjusted cost of
more than $40 million, was built on the back of a pair of IBM 7090s,
networked to more than 1000 reservation terminals in 50 cities via
10,000 miles of leased lines.

Reservation systems built on a similar architecture followed, and the
pattern began to spread to other industries. Visa developed its
electronic authorization and settlement systems, called BASE-I and
BASE-II, in a similar architecture, though notably the "terminals" in
BASE-I were actually DEC PDP-11s - far more powerful machines than the
teletypes used by SABRE.

With the development of these systems, system reliability became
geographical problem impacting thousands or millions of consumers
every day. The centralized computers in these systems were required by
the businesses they supported to be available; Lamport's famous quip
on distributed system - "[...] one in which the failure of a computer
you didn’t even know existed can render your own computer unusable" -
is nonsense before the advent of systems like SABRE and SAGE.

Fast forward to the 21st century, and these systems - more more
accurately, their descendants, are still in production. IBM's
Transaction Processing Facility, the descendant of SABRE, is still in
development and available to Z-series mainframe customers. In some
ways this style of architecture seems like a dead end - it's really
the antithesis of the currently dominant commodity approach to systems
design - but there is much to be learned from it, especially for those
interested in design and operations of stateful systems.
