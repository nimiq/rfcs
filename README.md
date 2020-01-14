# Nimiq RFCs

> The RFC process is currently only used for front-end related Nimiq repositories.

## What is an RFC?

The "RFC" (request for comments) process is intended to provide a
consistent and controlled path for new features to enter the framework.

Many changes, including bug fixes and documentation improvements can be
implemented and reviewed via the normal GitHub pull request workflow.

Some changes though are "substantial", and we ask that these be put
through a bit of a design process and produce a consensus among the Nimiq
team and the community.

## The RFC life-cycle

An RFC goes through the following stages:

- **Pending:** when the RFC is submitted as a PR.
- **Accepted:** when an RFC PR is merged and may undergo implementation.
- **Landed:** when an RFC's proposed changes are shipped in an actual release.
- **Rejected:** when an RFC PR is closed without being merged.

[Pending RFC List](https://github.com/nimiq/rfcs/pulls)

## When to follow this process

You need to follow this process if you intend to make "substantial"
changes to one of the projects listed below:

- [Nimiq Hub](https://github.com/nimiq/hub)
- [Nimiq Keyguard](https://github.com/nimiq/keyguard)

We are limiting the RFC process for these repos to test out the process in a
more manageable fashion, and may expand it to cover more projects under the
`nimiq` organization in the future. For now, if you wish to suggest changes to
those other projects, please use their respective issue lists.

What constitutes a "substantial" change is evolving based on community norms,
but may include the following:

- A new feature that creates new API surface area
- Changing the semantics or behavior of an existing API
- The removal of features that are already shipped as part of a release
- The introduction of new idiomatic usage or conventions, even if they do not
include code changes.

Some changes do not require an RFC:

- Additions that strictly improve objective, numerical quality criteria
(e.g. speedup, better browser support)
- Fixing objectively incorrect behavior
- Rephrasing, reorganizing or refactoring

If you submit a pull request to implement a new feature without going
through the RFC process, it may be closed with a polite request to
submit an RFC first.

## Why do you need to do this

It is great that you are considering suggesting new features or changes to
Nimiq - we appreciate your willingness to contribute! We want to receive better
and wider feedback on additions that the Team suggests itself, as well as
increasing cooperation with and among the Nimiq community in adding new features
to the Nimiq ecosystem. We hope to thereby foster community engagement and make
Nimiq development more decentralized.

The RFC process serves as a way to guide you through our thought process when
making changes to Nimiq, so that we can be on the same page when discussing why
or why not these changes should be made.

## Gathering feedback before submitting

It's often helpful to get feedback on your concept before diving into the
level of API design detail required for an RFC. **You may open an
issue on this repo to start a high-level discussion**, with the goal of
eventually formulating an RFC pull request with the specific implementation
design.

## What the process is

In short, to get a major feature added to Nimiq, one must first get the
RFC merged into the RFC repo as a markdown file. At that point the RFC
is 'accepted' and may be implemented with the goal of eventual inclusion
into Nimiq.

- Fork the RFC repo <http://github.com/nimiq/rfcs>

- Copy `0000-template.md` to `accepted-rfcs/0000-my-feature.md` (where
'my-feature' is descriptive). Don't assign an RFC number yet.

- Fill in the RFC template. Put care into the details: **RFCs that do not
present convincing motivation, demonstrate understanding of the
impact of the design, or are disingenuous about the drawbacks or
alternatives tend to be poorly-received**.

- Submit a pull request. As a pull request the RFC will receive design
feedback from the larger community, and the author should be prepared
to revise it in response.

- Build consensus and integrate feedback. RFCs that have broad support
are much more likely to make progress than those that don't receive any
comments.

- Eventually, the [team] will decide whether the RFC is a candidate
for inclusion in Nimiq.

- An RFC can be modified based upon feedback from the [team] and community.
Significant modifications may trigger a new final comment period.

- An RFC may be rejected after public discussion has settled
and comments have been made summarizing the rationale for rejection.
A member of the [team] then closes the RFC's associated pull request.

- An RFC may be accepted at the close of its final comment period. A [team]
member will merge the RFC's pull request, at which point the RFC will become
'accepted'.

_Changes to this RFC process can be suggested through issues on this
repository or by changing the process description in this README through
a pull request._

## Details on Accepted RFCs

Once an RFC gets accepted, authors may implement it and submit the
feature as a pull request to the respective Nimiq repo. Becoming 'accepted'
is not a rubber stamp, and does not mean that the team itself will implement
it. The status does also not guarantee that any implementation of it will
ultimately be merged. It means that the team has agreed to it in principle
and are interested to include a quality implementation of it.

Furthermore, the fact that a given RFC has been accepted implies nothing about
what priority is assigned to its implementation, nor whether anybody is
currently working on it.

Modifications to accepted RFCs can be done in followup PRs. We strive
to write each RFC in a manner that it will reflect the final design of
the feature; but the nature of the process means that we cannot expect
every merged RFC to actually reflect what the end result will be at
the time of release; therefore we try to keep each RFC
document somewhat in sync with the feature implementation as planned,
tracking such changes via followup pull requests to the document.

## Implementing an RFC

The author of an RFC is not obligated to implement it. Of course, the
RFC author (like any other developer) is welcome to post an
implementation for review after the RFC has been accepted.

An accepted RFC should have the link to the implementation PR listed if there
already is one. Feedback to the actual implementation should be conducted in the
implementation PR instead of the RFC PR.

If you are interested in working on the implementation for an 'accepted'
RFC, but cannot determine if someone else is already working on it,
feel free to ask (e.g. by leaving a comment on the associated PR or issue).

## Reviewing RFCs

Members of the [team] will attempt to review some set of open RFC
pull requests on a regular basis. If a team member believes an RFC PR is ready
to be accepted, they can approve the PR using GitHub's review feature to signal
their approval of the RFC.

**Nimiq's RFC process owes its inspiration to the [Vue RFC process].**

[Vue RFC process]: https://github.com/vuejs/rfcs
[team]: https://github.com/orgs/nimiq/people
