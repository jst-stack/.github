# Repository health signals

Every JST repository runs the same kinds of checks. This page states which signal blocks what, so a red badge, a warning, or a scheduled failure is read the same way in every repository.

## Blocks a pull request

- The repository `check` script: lint, unit tests, and `npm pack --dry-run` so the published file set stays valid.
- `npm run audit:production`: high and critical advisories in runtime dependencies.
- The development advisory ledger. Unreviewed development-only advisories are reported in the job log and stay non-blocking, because a new advisory in a build tool is a review task. Waivers that expired or that no longer match a reported advisory fail the job.
- The ESLint package matrix: ESLint 9 and 10 against Node.js 22.18.0 and 24.15.0 on Linux, macOS, and Windows.

## Blocks a release

- The release workflow publishes exactly the `package.json` version and refuses a version that `CHANGELOG.md` does not document.
- The released-artifact canary passes: projects are created and checked from the published CLI, template, and plugins with npm and pnpm.
- The compatibility contract holds: the template tag pinned by `create-jst`, `jst.compatibility.json`, the template `engines.node`, and the published package versions describe the same supported combination.

## Publishing an npm package

Releases are mechanical, so nobody tags or publishes by hand:

1. A pull request that changes published behaviour (`src`, `lib`, `rules`, or `bin` by default) must bump `package.json` and add a `## <version>` entry to `CHANGELOG.md`. The `npm release guard` workflow fails the pull request otherwise; documentation-only changes pass untouched.
2. After the merge, the `npm release` workflow runs the quality gate, skips versions that already exist in the registry, publishes the rest with provenance, waits until the registry serves the new version, then creates the `v<version>` tag and the GitHub release itself.
3. The workflow is idempotent: re-running it on an already published version only re-checks the artifact and re-creates a missing tag or release.

Both workflows live in `jst-stack/.github` and are called from each package repository, so the release path has one implementation.

## Release propagation SLO

| Signal | Target | If missed |
| --- | --- | --- |
| Released-artifact canary | Green within 24 hours of a release; the scheduled run is daily | Stop releasing until it is green again |
| Compatibility contract | Matches the pinned template at all times | Fix the manifest through the compatibility release workflow before the next release |
| High or critical production advisory | Remediated, or pinned with a documented reason, within 7 days | Blocks the next release |
| Development-only advisory | Reviewed within 30 days | Review task in the ledger; waivers expire after at most 180 days |

## Benchmarks

Benchmark jobs measure a pinned Node.js version, repeat the measurement, and compare the median against a stored baseline. A regression produces a warning first; only a regression above the blocking threshold fails the job. When the runner image or Node.js version changes, the baseline is recorded again with the new environment instead of raising the threshold.

## OpenSSF Scorecard

Scorecard is a radar, not a target. Read the checks that correspond to a decision we control:

- Pinned-Dependencies and Token-Permissions: actions are pinned by commit SHA and workflows request the narrowest permission set.
- Dangerous-Workflow: no workflow passes an untrusted expression to a shell step.
- Branch-Protection, Code-Review, Security-Policy, Signed-Releases: the repository settings we enforce for every project.

Ignored on purpose: CII-Best-Practices, Fuzzing, and Maintained. A score point that requires tooling we would not otherwise run, or a maintainer headcount we cannot change, is not worth a repository change. Fuzzing is added where it pays off, for example on the CLI argument parser, the migration path, and MCP input schemas; it is not added to raise a badge.

## What this means in practice

A scheduled failure is a defect in a process, not a reason to relax the threshold. Fix the measurement, the pin, or the gate; raising a limit or deleting a check moves the problem to users.
