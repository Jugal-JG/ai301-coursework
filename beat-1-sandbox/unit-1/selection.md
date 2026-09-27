# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/14

**Verdict output**

```
Re-graded #14 with the narrowed check:

- Repo not abandoned: pass — not archived, last push 2026-09-16 (11 days before today).
- Maintainer responds: pass — recent human-authored commit.
- Scope is one bounded change: pass — the maintainer already gave a concrete ask (named
  files: scripts/run_evals.py, rag/evaluator/eval_suite.py,
  tests/fixtures/sample_profiles/basic_profile.json; effort estimate 7-10h). The two open
  questions in the thread (does an actionability scorer belong here, where should the mock
  injection seam go) are implementation-approach choices, not disagreement over
  whether/what to build — so they don't trigger a scope fail under the narrowed check.
- Not already claimed: pass — no assignee, no open linked PR.
- AI-contribution policy: pass — silent in docs/CONTRIBUTING.md.
- Carries a good-first-issue signal (preferred): fail — labels are devops, enhancement,
  rag, tests, tier-3, no "good first issue" label.
- Related to AI/ML (preferred): pass — it's literally the repo's own RAG evaluation
  harness.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/14",
  "checks": [
    {"name": "Repo not abandoned", "grade": "pass", "evidence": "not archived; last push 2026-09-16, 11 days before capture"},
    {"name": "Maintainer responds", "grade": "pass", "evidence": "last commit human-authored (Aburke225) within 14 days"},
    {"name": "Scope is one bounded change", "grade": "pass", "evidence": "maintainer named exact files and gave a 7-10h estimate; open questions are implementation-approach only, not whether/what to build"},
    {"name": "Not already claimed", "grade": "pass", "evidence": "assignees: none; no linked PR"},
    {"name": "AI-contribution policy allows this workflow", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has no AI-use statement"},
    {"name": "Carries a good-first-issue signal", "grade": "fail", "evidence": "labels: devops, enhancement, rag, tests, tier-3 — no 'good first issue' label"},
    {"name": "Related to AI/ML", "grade": "pass", "evidence": "issue is the repo's RAG eval harness, labeled 'rag'"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. First full run: `agreement: 17/20 scored items  (bar: 18/20: below the bar)` —
   `issue-14` (gold accept) failed on `Maintainer responds` because its
   maintainer-response sample had only one issue with no reply; `issue-15` and
   `issue-20` (both gold reject) were wrongly accepted because the `Scope` check
   had no way to catch a years-old issue with abandoned closed PRs, or an
   unreviewed feature request with unresolved design gaps.
2. Second full run, after broadening `Maintainer responds` to also accept a
   recent human-authored commit as evidence of life, and adding two new `Scope`
   fail conditions (abandoned-PR history; unreviewed feature request with an
   unresolved design decision): `agreement: 18/20 scored items  (bar: 18/20: PASS)` —
   new misses appeared on `issue-09` and `issue-19` (both gold accept, now
   failing `Scope`).
3. Third full run, after narrowing the `Scope` check's design-debate clause to
   exclude a contributor's open implementation-approach question when the
   maintainer already gave a concrete ask (named files, an effort estimate) —
   this was needed so live-mode issue #14 (my chosen issue) would accept:
   `agreement: 18/20 scored items  (bar: 18/20: PASS)`. Same score and same two
   misses as run 2 — the narrowing didn't reopen anything already fixed. This is
   the run saved to `eval-run.txt`.

**Issue analysis**

`issue-19` (source: zxcalc/zxlive#517). Gold label: `accept` ("maintainer-diagnosed
performance bug with named causes, unclaimed"). My rubric's verdict: `reject`, failing
the `Scope is one bounded change` check. The issue body reads: "There are two potential
causes which should be fixed: 1. The matchers are slow... 2. UI update is waiting...
Additional suggestions: 1. We should use multi-processing... 2. Only match rewrites...
3. Applying the rewrite should also happen in a separate thread." My rubric's grader read
that as a list of several separable fixes/features (close to the tracking-issue pattern
my `Scope` check fails on), rather than one bounded performance bug with multiple
candidate root causes and possible approaches to try. The gold label treats it as one
issue (the UI freezes during large-subgraph selection) with a maintainer's diagnostic
notes attached, not an umbrella of independent work items. I haven't fixed this — it's a
real edge case my current wording doesn't distinguish cleanly from a genuine tracking
issue.

**Check rationale**

The `Not already claimed` check currently reads: "Fail if assignees is non-empty, or if
any linked PR is open. Stray claim comments in the thread (\"I'll take this\") do not by
themselves fail this check — only a formal assignee or an open linked PR counts as an
active claim." I weakened it from an earlier version that also failed on an undisputed,
recent claim comment. The reasoning: on Path Review (my scoped live-mode source),
`scope.md`'s house rule already says classmates' claim comments never block an issue,
since course credit attaches to opening a PR, not to merging first — so a check that
still rejected on comment-only claims would be fighting the house rule it's supposed to
respect, and would reject nearly every real issue in a shared classroom repo where
claim comments are the norm.

**Trade-offs**

This shows up concretely in live mode: issue #69
(https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69) has a classmate's
comment ("I'd like to attempt this!") with a fairly detailed stated plan, but no
assignee and no open PR yet. Under the weakened check my rubric still graded `Not
already claimed` as pass and accepted the issue. Under the stricter version I dropped
(fail on any undisputed recent claim comment), it would have rejected #69 outright. The
trade-off: my rubric now accepts issues where a classmate is actively mid-plan on the
exact same bug, which in a normal open-source project would be a real duplicated-effort
risk — but that risk is exactly what the Path Review house rule says not to weigh, so I
accept it here on purpose.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. I've built something similar to this before (an offline eval/scoring harness for a
   RAG-style pipeline) in a previous course, so I chose a tier-3/advanced issue on
   purpose instead of a quicker tier-1 fix — it lets me apply that existing experience
   rather than start from a trivial doc or config fix, even though it's estimated at
   7-10 hours rather than 1-4.
2. The verdict correctly identified that the repo is alive, unclaimed (no assignee or
   open PR), and that the AI-contribution policy doesn't block my workflow. What the
   rubric couldn't weigh is my own background: it flagged two open implementation
   questions in the thread (whether an actionability scorer belongs in scope, and where
   to add a mock-LLM injection seam) as a scope risk, and I had to judge myself that
   these are ordinary implementation decisions I'm equipped to make, not a sign the
   issue itself is unscoped — the maintainer already gave concrete files and an effort
   estimate.
3. I expect the main difficulty to be the injection-seam design (`ReviewGenerator`
   builds its own `openai.OpenAI` client internally with no seam for a mock), since
   that has to be solved before the eval suite can be tested without live API calls.
   A classmate is already partway through reproduction and has asked the maintainer the
   same two questions I noticed, so I may need to coordinate with them or wait briefly
   for a maintainer answer before committing to one approach.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
