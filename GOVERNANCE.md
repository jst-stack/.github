# Governance

JST Stack uses lightweight maintainer governance. Technical decisions optimize for explicit ownership, replaceable effects, enforceable boundaries, and a low cost of understanding.

## Roles

- **Contributor:** reports, documents, reviews, or ships a focused change.
- **Reviewer:** has repeated, high-quality contributions and may triage issues and review changes in a demonstrated area.
- **Maintainer:** owns releases, security response, repository policy, and final decisions.

Maintainers nominate reviewers and maintainers based on sustained judgment, reliability, and constructive participation. A role may be removed after prolonged inactivity, a security concern, or repeated conduct violations. The current maintainers are listed in `MAINTAINERS.md`.

## Decisions

Routine fixes use issues and pull requests. A breaking public API, architecture-policy change, new package, governance change, or long-lived exception starts as an RFC in [JST Discussions](https://github.com/jst-stack/jst/discussions). The proposal must state the problem, constraints, alternatives, compatibility impact, and migration plan.

Maintainers seek consensus. When consensus is not possible, the maintainer responsible for the affected repository records the decision and rationale. Sponsorship never purchases a technical decision.

## Releases and security

Only maintainers may approve protected release environments. Security reports stay private until a fix and coordinated disclosure are ready. Emergency changes may use the repository ruleset bypass, must preserve an audit trail, and receive a follow-up review.

## Continuity

When a maintainer expects to be unavailable, release and security access should be transferred to another active maintainer. Until JST has a second maintainer, the project explicitly has a bus-factor risk and does not promise an availability SLA.
