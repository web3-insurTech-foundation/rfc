# web3-insurTech Specification Contribution Guide

web3-insurtech is still an evolving language. This repository contains the
specification text as well as Pull Requests with suggested improvements and
contributions.

Contributions that do not change the interpretation of the spec but instead
improve legibility, fix editorial errors, clear up ambiguity and improve
examples are encouraged and are often merged by a spec editor with little
process.

However, contributions that _do_ meaningfully change the interpretation of the
spec must follow an RFC (Request For Comments) process led by a _champion_
through a series of _stages_ intended to improve _visibility_, allow for
_discussion_ to reach the best solution, and arrive at _consensus_. This process
becomes ever more important as GraphQL's community broadens.

When proposing or weighing in on any issue or pull request, consider the
[Code of Conduct](https://github.com/web3-insurTech-foundation/rfc/blob/main/CODE-OF-CONDUCT.md)
to better understand expected and unacceptable behavior.


## RFC Contribution Champions

Contributing to web3-insurtech requires a lot of dedicated work. To set clear
expectations and provide accountability, each proposed RFC (request for
comments) must have a _champion_ who is responsible for addressing feedback and
completing next steps. An RFC may have multiple _champions_. The spec editors
are not responsible for completing RFCs which lack a _champion_ (though an
editor may be a _champion_ for an RFC).

An RFC which does not have a _champion_ may not progress through stages, and can
become stale. Stale proposals may be picked up by a new _champion_ or may be
_rejected_.

## RFC Contribution Stages

RFCs are guided by a _champion_ through a series of stages: _strawman_,
_proposal_, _draft_, and _accepted_ (or _rejected_), each of which has suggested
entrance criteria and next steps detailed below. RFCs typically advance one
stage at a time, but may advance multiple stages at a time. Stage advancements
typically occur during [Working Group](https://github.com/web3-insurTech-foundation/w3if-wg)
meetings, but may also occur on GitHub.

In general, it's preferable to start with a pull request so that we can best
evaluate the RFC in detail. However, starting with an issue is also permitted if
the full details are not worked out.

All RFCs start as either a _strawman_ or _proposal_.

## Stage 0: _Strawman_

An RFC at the _strawman_ stage captures a described problem or
partially-considered solutions. A _strawman_ does not need to meet any entrance
criteria. A _strawman's_ goal is to prove or disprove a problem and guide
the discussion toward either rejection or a preferred solution. A _strawman_ may be
an issue or a pull request (though an illustrative pull request is preferable).

_There is no entrance criteria for a Strawman_

As implied by the name
[strawman](https://en.wikipedia.org/wiki/Straw_man_proposal), the goal at this
stage is to knock it down (_reject_) by considering other possible related
solutions, showing that the motivating problem can be solved with no change to
the specification, or that it is not aligned with the _guiding principles_.

Once it is determined that the _strawman_ is compelling, it should seek the entrance
criteria for _proposal_.

## Stage 1: _Proposal_

An RFC at the _proposal_ stage is a solution to a problem with enough fidelity
to be discussed in detail. It must be backed by a willing _champion_. A
_proposal_'s goal is to make a compelling case for acceptance by describing both
the problem and the solution via examples and spec edits. A _proposal_ should be
a pull request.

_Entrance criteria:_

- Identified _champion_
- Clear explanation of the problem and solution
- Illustrative examples
- Incomplete spec edits
- Identification of potential concerns, challenges, and drawbacks

A _proposal_ is subject to the same discussion as a _strawman_: ensuring that it
is well aligned with the _guiding principles_, is a problem worth solving, and
is the preferred solution to that problem. A _champion_ is not expected to have
confidence in every detail at this stage and should instead focus on identifying
and resolving issues and edge-cases. To better understand the technical
ramifications of the _proposal_, a _champion_ is encouraged to implement it in a
GraphQL library.

Most _proposals_ are expected to evolve or change and may be rejected.
Therefore, it is unwise to rely on a _proposal_ in a production GraphQL service.
GraphQL libraries _may_ implement _proposals_, though they are encouraged not to
enable the _proposed_ feature without explicit opt-in.

## Stage 2: _Draft_

An RFC at the _draft_ stage is a fully formed solution. There is a working group
consensus the problem identified should be solved, and this particular solution
is preferred. A _draft's_ goal is to precisely and completely describe the
solution and resolve any concerns through library implementations. A _draft_
must be a pull request.

_Entrance criteria:_

- Consensus the solution is preferred (typically via Working Group)
- Resolution of identified concerns and challenges
- Precisely described with spec edits
- Compliant implementation in GraphQL.js (might not be merged)

A _proposal_ becomes a _draft_ when the set of problems or drawbacks have been
fully considered and accepted or resolved, and the solution is deemed desirable.
A _draft_'s goal is to complete final spec edits that are ready to be merged and
implement the _draft_ in GraphQL libraries along with tests to gain confidence
that the spec text is sufficient.

_Drafts_ may continue to evolve and change, occasionally dramatically, and are
not guaranteed to be accepted. Therefore, it is unwise to rely on a _draft_ in a
production GraphQL Service. GraphQL libraries _should_ implement _drafts_ to
provide valuable feedback, though are encouraged not to enable the _draft_
feature without explicit opt-in when possible.

## Stage 3: _Accepted_

An RFC at the _accepted_ stage is a completed solution. According to a spec
editor it is ready to be merged as-is into the spec document. The RFC is ready
to be deployed in GraphQL libraries. An _accepted_ RFC must be implemented in
GraphQL.js.

_Entrance criteria:_

- Consensus the solution is complete (via editor or working group)
- Complete spec edits, including examples and prose
- Compliant implementation in GraphQL.js (fully tested and merged or ready to
  merge)

A _draft_ is _accepted_ when the working group or editor has been convinced via
implementations and tests that it appropriately handles all edge cases; that the
spec changes not only precisely describe the new syntax and semantics but
include sufficient motivating prose and examples; and that the RFC includes
edits to any other affected areas of the spec. Once _accepted_, its _champion_
should encourage adoption of the RFC by opening issues or pull requests on other
popular GraphQL libraries.

An _accepted_ RFC is merged into the GraphQL spec's main branch by an editor and
will be included in the next released revision.

## Stage X: _Rejected_

An RFC may be _rejected_ at any point and for any reason. Most rejections occur
when a _strawman_ is proven to be unnecessary, is misaligned with the _guiding
principles_, or fails to meet the entrance criteria to become a _proposal_. A
_proposal_ may become _rejected_ for similar reasons as well as if it fails to
reach consensus or loses the confidence of its _champion_. Likewise a _draft_
may encounter unforeseen issues during implementations which cause it to lose
consensus or the confidence of its _champion_.

RFCs which have lost a _champion_ will not be _rejected_ immediately, but may
become _rejected_ if they fail to attract a new _champion_.

Once _rejected_, an RFC will typically not be reconsidered. Reconsideration is
possible if a _champion_ believes the original reason for rejection no longer
applies due to new circumstances or new evidence.
