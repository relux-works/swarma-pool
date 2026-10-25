# Swarma Pool

Status: Draft (2026-10-25).

Specifications for ephemeral isolated execution of third-party code on dedicated
pool hosts. This repository contains specifications and research first, with no
implementation or tooling yet. Admission is closed: no host, backend tuple,
public matrix row or job is activated by publication.

The shared [Execution Isolation Contract, CIP-0025](https://github.com/relux-works/curator-spec/blob/dea17185c0317892c13dda823baaeded684f6352/cips/CIP-0025-execution-isolation-contract.md)
defines isolation profiles, bindings, receipts and qualification. The
[foreign-code execution contract, CIP-0026](https://github.com/relux-works/curator-spec/blob/88b0d8615a5df8728e0e9540a9edd708c0ca5660/cips/CIP-0026-foreign-code-execution.md)
defines the pool's execution, custody and admission obligations. These documents
refine that pool class without granting station or platform-service authority.
The stock Firecracker cold-boot path remains PENDING-VERIFY for the required
all-thread filter ordering; admission stays closed pending the stated owner
decisions and exact-tuple qualification.

## Documents

| Document | Purpose |
|---|---|
| [Threat model](research/threat-model.md) | Adversaries, assets, threats, controls and external interpretation inventory. |
| [Isolation backend evidence](research/isolation-backends.md) | Pinned primary sources, bounded comparisons, limitations and unrun acceptance proposals. |
| [Backend choice](research/backend-choice.md) | Provisional qualification target, required properties and open owner choices. |
| [Runner hosts](spec/runner-hosts.md) | Dedicated-host operation, disposable units, lifecycle, cleanup and acceptance cases. |
| [Pool qualification](spec/pool-qualification.md) | Host admission, authority, evidence, qualification and activation gates. |

## Open questions

[UNRESOLVED_QUESTIONS.md](UNRESOLVED_QUESTIONS.md) retains the open decisions for
the platform owners. Recommendations and documentary acceptance do not authorize
activation; unknown or missing required evidence remains unavailable.

## Tools

The repository has no tooling yet. Publication links were checked with Python 3
and markdown-it-py by resolving relative files and heading fragments and checking
that external hyperlinks name full commit revisions. The GitHub CLI checked public
repository visibility and immutable file existence with
`gh api repos/OWNER/REPO/contents/PATH?ref=FULL_SHA`; named tags were resolved with
`gh api repos/OWNER/REPO/commits/TAG`. Snapshot references without public commit
pins remain explicitly unlinked with their original content identities.
Git checks used `git diff --no-index SOURCE PORT` for the publication delta and
`git diff --cached --check` for whitespace. Check scripts, logs and delta records
were kept outside this repository. These are document checks, not runtime or
qualification evidence; the described acceptance cases remain unrun.

## License

[Apache License 2.0](LICENSE).
