# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Repo not abandoned | Repo facts: `archived:` flag, `last push to any branch`, `last 5 default-branch commits` dates, `latest release` date | Fail if `archived: yes`. Otherwise pass only if at least one of `last push to any branch` or a default-branch commit is dated within 180 days of the capture date (live mode: within 180 days of today). | required |
| Maintainer responds | Repo facts: `maintainer first-response sample` (recently updated issues, days to first owner/member/collaborator comment); `last 5 default-branch commits` (author names) | Pass if at least 1 sampled issue shows a first maintainer response in 45 days or fewer. Also pass, even if every sampled issue shows no response or the sample has fewer than 3 issues, when at least one of the last 5 default-branch commits is authored by a named human (not a `[bot]` account) within 14 days of the capture date — frequent human commits are equally strong evidence the maintainer is present, and a small/quiet repo may not generate enough issue traffic to sample a response. Fail only if neither condition holds. | required |
| Scope is one bounded change | The issue body, comment thread, open date, and linked-PR history | Fail if any of: (a) the issue explicitly frames itself as a tracking/meta issue whose listed items are meant to become separate issues or PRs (words like "tracking issue", "meta-issue", "split into", or a list of otherwise-unrelated features/bugs each independently issue-worthy); (b) the thread shows unresolved disagreement or debate over *whether* the change is wanted or *what* its scope/behavior should be, with no maintainer-settled spec — this does NOT include a contributor's open question about implementation approach (which library, where to add a seam, how to structure the fix) when the maintainer has already stated a concrete ask, such as naming the files to change or giving an effort estimate; that is a normal engineering decision, not a scope failure; (c) it is a usage/support question ("how do I...") rather than a requested change; (d) a maintainer states the fix requires touching core internals/architecture; (e) the issue has been open for a year or more AND has one or more closed, unmerged linked PRs in its history — abandoned attempts are evidence the real difficulty exceeds its labeling, regardless of current claim status; (f) it is a feature request/enhancement with no maintainer response anywhere in the thread whose own body leaves a core design decision unresolved (an unchecked pre-submission/roadmap checkbox, a "TBD" placeholder, or hedging like "possibly... if needed") — a proposal still awaiting scoping is not yet a bounded task, unlike a fully specified change list with no open decisions. A single feature or fix that touches several files to implement one cohesive, fully specified piece of work is NOT a scope failure, even with a multi-item checklist or zero comments. A terse body is also not itself a scope failure. | required |
| Not already claimed | `this issue: assignees:` and linked-PR state | Fail if assignees is non-empty, or if any linked PR is open. Stray claim comments in the thread ("I'll take this") do not by themselves fail this check — only a formal assignee or an open linked PR counts as an active claim. | required |
| AI-contribution policy allows this workflow | `contribution policy` line (quoted from CONTRIBUTING.md / linked docs) | Fail only on an outright ban on AI-generated code or documentation. Pass if the policy states conditions (disclosure, human review, testing) or is silent on AI use. | required |
| Carries a good-first-issue signal | The issue's `labels:` line | Pass if a label naming the issue as beginner-friendly (e.g. `good first issue`, `good-first-issue`, `help wanted` combined with a size hint) is present. | preferred |
| Related to AI/ML | The repo's description, and the issue's title and body | Pass if the repo description, issue title, or issue body signals an AI/ML/data-science domain (e.g. mentions machine learning, LLMs, a model, a dataset, training/inference, or a framework such as PyTorch, TensorFlow, scikit-learn, Hugging Face). Fail otherwise. Never gates the verdict — only ranks accepted issues, putting AI/ML-flavored ones first. | preferred |

## Verdict rule

Accept only if every `required` check passes. Any `required` check graded
`fail` or `unclear` rejects the issue — `unclear` is treated as `fail`
because a first issue you cannot verify is not a first issue you should
take. `preferred` checks never change the verdict; use them only to rank
issues that already pass every required check.
