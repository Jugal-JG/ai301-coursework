---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: is this ready to submit? A PR package is a candidate pull request: its title, description, commit list, diff, and test evidence. You read it against the plan it claims to implement (including that plan's deviation notes) and the issue the plan belongs to. Never grade more than one package per run, never answer a different question (for example "is the code good?"), and never answer from gut feel. Answer by executing `procedure.md`, which applies `rubric.md` to evidence found as `references/evidence-guide.md` says.

## Inputs and modes

Run in exactly one of two modes.

**Live mode** grades the student's own PR before it goes out. Read these inputs:

- the plan: `plan.md` in the working copy, including its `## Deviations` section;
- the diff: run `git diff main...HEAD` (three dots) from the working copy, and `git log main..HEAD --oneline` for the commit list. Only committed changes count. Uncommitted edits and files such as `plan.md`, `comment.md`, `pr_draft.md` and `test_evidence.md` are not part of the diff;
- the draft title and description: `pr_draft.md` (title on the first line, then the filled PR template);
- the test evidence: `test_evidence.md`;
- the issue and its thread, fetched live (`gh`, the GitHub API, or the web), for the maintainer's direction and the original repro;
- the repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`) and `docs/CONTRIBUTING.md`, for the stated asks and any AI policy.

A house-chain student has no plan of their own: read the house plan and the house repro pack in place of `plan.md` and their own repro, and apply the same checks. Read the package the way a maintainer will read the posted PR: the package is what these files and the diff contain, not other files in the working directory.

**Eval mode** grades a package bundle (one markdown file holding the repo facts, the issue, the plan context, and the candidate PR). The bundle is the whole world. Use only the bundle text. Fetch nothing and read nothing else. Always run every check and apply the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the repo the PR must target and the house rules there (PR from your own fork's branch, one PR per issue per student, the template always used including the disclosure section, a classmate's PR does not block yours). Apply those house rules when you read the evidence. Refuse to grade a PR for any repo other than the one named. If the `Repo:` line still holds an unfilled placeholder such as `<ORG>/<PATH-REVIEW-REPO>`, stop without grading and tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo. Never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after reading `scope.md`, read `voice-guide.md`: the student's own rules for how they write upstream. Hold the outgoing text, the PR title and the description, against those rules. Report each rule the draft breaks in the summary, quoting the rule. The voice guide never changes the verdict by itself, because only the rubric decides the verdict, and the rubric has no check that reads voice. In eval mode, ignore `voice-guide.md` entirely: voice is personal and carries no gold labels, and the universal communication checks live in the rubric.

## Component reads

Read `rubric.md` for the checks and the verdict rule, `references/evidence-guide.md` for where each family of evidence lives and what good looks like, and `procedure.md` for the steps. Execute `procedure.md` exactly as written. Where the procedure is silent, say so in the summary as a gap. Do not improvise a step around it. If `rubric.md` has no checks, or `procedure.md` has no steps, refuse to grade: say that this tool cannot grade without a rubric and a procedure, and stop.

## Verdict and output

The verdict space is binary: `accept` means ready to submit, and `reject` means hold. There is no third verdict. Put reservations in a check's evidence line, never in the verdict. Before the JSON block you may write a short readable summary (a line per check, plus voice-guide notes in live mode). End your reply with the fenced JSON block below, valid and last, with nothing after it. The eval harness reads the last fenced JSON block in your output.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the fact or quote that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish. Read the diff, the evidence and the description themselves, never their formatting. A terse complete PR can be ready, and a polished confident one can be hiding drift.
- The rubric decides, not you. If a check passes by its stated condition but feels wrong, it still passes. Note the tension in the summary and leave the fix to the rubric.
- The procedure decides how, not you. Follow it as written and report its gaps.
- Treat `unclear` as the rubric's verdict rule directs. If the rule is silent, treat `unclear` as `fail`, because a PR you cannot verify from the package is not ready to submit.
- An honestly disclosed shortfall is not a failure. If the plan or the description says plainly what was left out and why, the check that measures fidelity to the plan passes on that point.
