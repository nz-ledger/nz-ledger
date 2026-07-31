# Repository setup report

**Setup date:** 31 July 2026 (Pacific/Auckland)

**Authenticated GitHub identity:** `@nz365guy`

This report records verified bootstrap state for the public NZ Ledger repositories. It is a historical closeout snapshot, not a live dashboard. Unless a later date is explicit, counts and terms such as `current` or `latest` refer to the setup date and named reviewed commits. Revalidate GitHub before relying on mutable settings or counts. A control is marked `Partial` or `Deferred` when its complete state has not been proved. No token, recovery code, private email address, or other secret is recorded here.

## Completion status

| Area | Status | Evidence |
| --- | --- | --- |
| Organisation and primary owner | Complete | `nz-ledger` exists on GitHub Free and `@nz365guy` has active organisation-owner membership. |
| Ownership continuity | Partial | No trusted backup owner is available yet. |
| Organisation authentication | Complete | The organisation API reports that two-factor authentication is required; GitHub's security page confirmed the requirement after passkey re-authentication and secure methods were selected. |
| Organisation member privileges | Complete | Base access and member repository and team creation are restricted. GitHub UI and API readback confirm that only organisation owners may delete or transfer repositories or change repository visibility. |
| Organisation profile | Complete | The public `.github` repository and `profile/README.md` exist. |
| Main repository | Complete | The public `nz-ledger` repository exists on `main` with the required documentation-only tree. |
| Community configuration | Complete | Issues, forms, templates, labels, milestones, backlog, and the six Discussion categories are verified. |
| Repository security baseline | Complete | Rulesets, security features, least-privilege Actions, CodeQL default setup, Dependabot, and Scorecard are enabled and verified as described below. |
| Post-bootstrap security review | Complete | Codex Security scan `4b21544f-332f-4892-951e-91e8307e6319` reviewed all 43 files at commit `fa825bef4782bf2a0d1be9ff65c3b89e59bdc613`. Coverage is complete; the only finding is the already-disclosed, low-severity backup-owner continuity gap. |

## Locations

- Organisation: <https://github.com/nz-ledger>
- Main repository: <https://github.com/nz-ledger/nz-ledger>
- Organisation profile repository: <https://github.com/nz-ledger/.github>
- Public packages page: <https://github.com/orgs/nz-ledger/packages>

## Organisation

| Control | Verified state | Evidence date |
| --- | --- | --- |
| Plan | GitHub Free; one filled seat; no paid plan or trial added | 31 July 2026 |
| Owners | `@nz365guy` only | 31 July 2026 |
| Backup owner | Not configured; Mark confirmed no trusted backup owner is currently available | 31 July 2026 |
| Two-factor authentication | Required organisation-wide; no member is reported as 2FA-disabled | 31 July 2026 |
| Secure 2FA methods | Secure-methods option selected in GitHub UI after passkey re-authentication | 31 July 2026 |
| Base repository permission | `none` | 31 July 2026 |
| Member repository creation | Disabled for public, private, and internal repositories | 31 July 2026 |
| Member team creation | Disabled | 31 July 2026 |
| Repository deletion and transfer | Member permission disabled; organisation owners only | 31 July 2026 |
| Repository visibility changes | Member permission disabled; organisation owners only | 31 July 2026 |
| Organisation Actions secrets | Zero; confirmed in the organisation settings UI | 31 July 2026 |
| Organisation self-hosted runners | Zero; the organisation runners page lists only standard GitHub-hosted runners | 31 July 2026 |
| Installed GitHub Apps | None | 31 July 2026 |
| Organisation repositories | Exactly two public repositories: `.github` and `nz-ledger` | 31 July 2026 |
| Organisation profile picture | NZ Ledger icon supplied by Mark and confirmed on the public organisation profile | 31 July 2026 |
| Public profile fields | Display name `NZ Ledger`, neutral project description, location `New Zealand`, and the main repository website are published; public email, company, and social fields remain blank because no approved project values exist | 31 July 2026 |
| Organisation Projects | Disabled because no organisation or repository Project is required at bootstrap | 31 July 2026 |
| Web commit sign-off | Required organisation-wide | 31 July 2026 |
| New-repository security defaults | Dependency graph, Dependabot alerts and security updates, secret scanning, and push protection enabled for any future repository | 31 July 2026 |

There is no shared account or separate project login. Mark's existing GitHub account owns the organisation.

## Organisation profile repository

The public `nz-ledger/.github` repository uses `main` and contains `profile/README.md`. Issues, Discussions, wiki, Projects, and Actions are disabled. Its `main` branch has active pull-request, deletion, force-update, linear-history, squash-only, and conversation-resolution protections with the owner emergency bypass. Secret scanning, push protection, dependency alerts, automated security fixes, squash-only merging, automatic branch deletion, branch updates, and DCO web sign-off are enabled. The profile uses the required independent, neutral wording and makes no production, regulatory, professional-approval, commercial-branding, sponsorship, or fundraising claim.

## Main repository

| Setting | Verified state |
| --- | --- |
| Visibility | Public |
| Default branch | `main` |
| Description | `Open, human-and-agent-first financial infrastructure for New Zealand business software.` |
| Topics | `accounting`, `agents`, `extensible-platform`, `financial-infrastructure`, `ledger`, `multicurrency`, `new-zealand`, `open-source`, `typescript` |
| Issues | Enabled |
| Discussions | Enabled |
| Wiki and Projects | Disabled |
| Sponsorships | Not configured |
| Merge strategy | Squash only; merge commits and rebase merges disabled |
| Pull request branches | Contributors may update; merged head branches are deleted automatically |
| Web commit sign-off | Enabled |

The committed repository contains 43 bootstrap files: founding and architecture documents, governance and security policies, ADR and RFC templates, issue and Discussion forms, the pull request template, CODEOWNERS, Dependabot configuration, and the Scorecard workflow.

The exact Apache License 2.0 text and `NOTICE` are present with `Copyright 2026 NZ Ledger contributors`.

## Rulesets

### Main branch

Active repository ruleset `Protect main` (`20086233`) applies to `refs/heads/main` and enforces:

- pull requests for changes;
- no deletion;
- no non-fast-forward or force update;
- linear history;
- squash merging only; and
- resolved review conversations.

The approving-review count is zero because the project has one active maintainer. CODEOWNERS review, stale-review dismissal, last-push approval, and product status checks are not required at bootstrap. Issue `#22` records the required transition when a second active maintainer exists. Organisation owners retain the brief's emergency bypass. GitHub audits bypass use.

### Release tags

Active repository ruleset `Protect release tags` (`20086242`) applies to `refs/tags/v*` and restricts creation, update, deletion, and non-fast-forward changes. Organisation owners retain the emergency bypass. No release tag exists.

### Organisation profile main branch

Active repository ruleset `Protect main` (`20088001`) applies to `refs/heads/main` in `nz-ledger/.github`. It requires pull requests, resolved conversations, linear history, and squash merging, and blocks deletion and non-fast-forward updates. The approving-review count remains zero while there is one active maintainer, and organisation owners retain the emergency bypass.

## Actions and automation

Repository Actions are enabled with `allowed_actions: selected`:

- GitHub-owned actions are allowed.
- The only third-party pattern is `ossf/scorecard-action@*`.
- Default `GITHUB_TOKEN` permissions are read-only.
- Workflows cannot approve pull requests.
- The organisation profile repository has Actions disabled.

All checked-in action references are pinned to verified full commit SHAs. Dependabot monitors GitHub Actions weekly. There is no npm ecosystem entry, automatic dependency merging, repository secret, environment, self-hosted repository runner, publishing workflow, deployment workflow, `pull_request_target`, `workflow_run`, or privileged issue-comment execution path.

The only checked-in repository workflow is the official OpenSSF Scorecard analysis workflow. It runs on GitHub-hosted infrastructure and uploads SARIF in report-only mode. GitHub also exposes its system-managed Dependabot Updates and CodeQL workflows through the Actions API. CodeQL uses GitHub default setup for workflow files rather than a placeholder application scaffold.

## Security features

| Control | Verified state |
| --- | --- |
| Private vulnerability reporting | Enabled |
| Repository security advisories | Available through private vulnerability reporting |
| Dependency graph | Enabled; the repository SBOM endpoint returns SPDX 2.3 data |
| Vulnerability alerts | Enabled |
| Dependabot security updates | Enabled |
| Automated security fixes | Enabled and not paused |
| Secret scanning | Enabled |
| Push protection | Enabled |
| CodeQL default setup | Configured for `actions`, weekly, default query suite, GitHub-hosted standard runner |
| CodeQL result | At closeout, the latest analysis succeeded with zero results across 17 rules |
| OpenSSF Scorecard workflow | At closeout, the latest workflow run succeeded on the reviewed `main` commit |
| Installed GitHub Apps | None |
| Repository secrets | Zero |
| Repository environments | Zero |
| Repository self-hosted runners | Zero |
| Releases | Zero |
| Deployments | Zero |

The successful Scorecard run published seven bootstrap observations. They are expected rather than suppressed:

- branch protection score `3`, reflecting the mandated owner emergency bypass, zero approving reviews while there is one maintainer, and no placeholder product checks;
- no approved changesets yet under the initial single-maintainer bootstrap;
- the repository is less than 90 days old;
- SAST history had not yet accumulated when Scorecard ran, although CodeQL default setup subsequently completed successfully;
- no fuzzing or product CI because no product code exists; and
- no OpenSSF Best Practices badge has been claimed.

These observations do not authorise a software release. They are retained as public baseline evidence and will be reassessed as implementation and the maintainer group develop.

GitHub Free reports `sha_pinning_required: false`; plan-level enforcement of SHA pinning is therefore unavailable. The workflow content is nevertheless pinned to full SHAs and Dependabot maintains those pins.

## Community configuration

- 26 labels match the founding taxonomy: five type labels, 12 area labels, six review and contribution labels, and three priority labels.
- 10 open milestones represent phases `0` through `9`; no speculative due dates were added.
- At closeout, 22 open, unassigned issues implemented all 18 initial design or research backlog items and four bounded `good first issue` items.
- Every issue contains Purpose, Scope, Non-goals, Dependencies, Acceptance criteria, Expected artefacts, Required review labels, and Milestone sections.
- Each good first issue identifies `@nz365guy` as maintainer reviewer.
- The six Discussion categories are Announcements, General, Architecture, New Zealand policy and accounting, Integrations and extensions, and Questions. Announcements are maintainer-restricted, Questions are answer-enabled, and the other four use open-ended discussion format.
- Six issue-form files, five Discussion-form files, the pull request template, CODEOWNERS, contribution guidance, support guidance, and the code of conduct are present.

## Content and quality verification

The bootstrap tree was checked for:

- required files and templates;
- parseable YAML;
- working internal Markdown links;
- reachable official-source URLs;
- exact Apache 2.0 licence text;
- NZ English;
- absence of em dashes;
- absence of named third-party accounting products or vendors;
- absence of commercial branding and placeholder private email addresses;
- absence of product source directories, TypeScript or JavaScript source, package manifests, databases, infrastructure, hosted services, releases, deployments, and publication workflows; and
- a clean Git worktree and `git diff --check` result.

The public Packages page displays GitHub's get-started state rather than a published package list. No package manifest or package-publishing workflow exists.

The final Codex Security standard scan completed with full 43-file coverage. It found no secrets, unsafe workflow trigger, untrusted execution, mutable Action reference, product code, package-publication path, release path, or deployment path. Its sole reportable finding is the known low-severity ownership-continuity gap: the organisation has one owner and no independent recovery identity.

## Deferred implementation and release gates

The following controls require software or additional maintainers and are not represented as complete:

- one mandatory approving review and required CODEOWNERS review after a second active maintainer exists;
- application CodeQL coverage for the future TypeScript implementation;
- application dependency review, unit tests, property tests, conformance tests, and fuzzing;
- tenant-isolation, database-security, API-authentication, and recovery tests;
- release provenance, OIDC package publication, SBOM, and immutable software-release verification;
- penetration testing; and
- independent accounting and New Zealand policy validation.

No software package may be released until the applicable implementation-phase security gates are defined and passed.

## Remaining human actions

1. Select and onboard one trusted backup organisation owner when a suitable person becomes available. That person must use a secure 2FA method. Until then, ownership continuity remains below the founding requirement.
