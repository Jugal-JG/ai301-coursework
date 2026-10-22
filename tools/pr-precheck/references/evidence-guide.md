# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: eval bundle: the `## Plan context` block holds the plan's files, approach steps, not-in-scope items, deviation notes and repro; the `### Diff` block holds what the PR changes; `### Description` holds the claims about it. Live mode: `plan.md` (with `## Deviations`) in the working copy, the output of `git diff main...HEAD`, and the draft in `pr_draft.md`.

What good looks like: every file and hunk in the diff is covered by the plan or a deviation note, nothing the plan scopes out appears, and every promised step is in the diff or stated as deferred. The description's claims about the change are true of the diff. The tell for drift is a description that says "exactly as planned" or "no changes beyond the fix" above a diff with an extra option, a rename pass, a rewritten function or an unplanned file, or a description that says docs were updated when the diff has none. A deferral written in the plan or the description, with a reason, is not drift.

## Test evidence (harness category: not-tested)

Where it lives: eval bundle: `### Test evidence`, read against the plan's test plan in `## Plan context`. Live mode: `test_evidence.md` next to `plan.md`, read against the test plan in `plan.md` and the Testing section of `pr_draft.md`.

What good looks like: the plan's repro command appears with its output before the fix and after it, and the two differ in the way the plan predicted (an exit code, a rendered result, a test that failed and now passes). Every failure mode the plan's test plan names is shown, or the plan or description says which one was left out. The repo's own named checks (for example `make test-unit`, `make lint`, `make typecheck`) appear with their real output, and a failing one is reported with the reason. Not decisive: "tested locally", "tests pass" with nothing named, evidence that runs the unchanged path (a control, a different input), or a new test that does not exercise the fixed behavior.

## Diff quality (harness category: unreviewable)

Where it lives: eval bundle: the added lines (`+`) in `### Diff` and the commit lines under `### Commits`. Live mode: `git diff main...HEAD` (use `--stat` to list files) and `git log main..HEAD --oneline`.

What good looks like: the diff holds only the change. Debris looks like a debug print or log line added to trace the bug, commented-out code, a dead or unused function or import added by the PR, re-indented or reformatted hunks that change no behavior, and hunks in files unrelated to the fix. Placeholder commits ("wip", "misc cleanups while debugging") are a hint that debris may be present. An import or helper the fix really uses is not debris.

## Standards and comms (harness category: standards-wall)

Where it lives: eval bundle: the `## Repo facts` lines for the pull-request template and the contribution policy (including any AI-use rule), read against `### Description`, `### Title`, and the files in `### Diff`. Live mode: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`, read against the filled template in `pr_draft.md` and the diff.

What good looks like: every section the template asks for has real content (Summary, `Closes #<issue>`, one Changes bullet per change, Testing boxes ticked only where the evidence supports them, Screenshots, Notes for Reviewers). Anything the repo's policy requires of a PR is present in the diff, for example a changelog entry or a test. When the policy explicitly requires disclosing AI use, the description says so in plain words, with the tool and what it did. A policy that only permits AI or only makes the human responsible for review does not require a statement. In live mode the house rules in `scope.md` apply too: the PR is from the student's fork branch, and the course always asks for the AI-use disclosure under Notes for Reviewers.
