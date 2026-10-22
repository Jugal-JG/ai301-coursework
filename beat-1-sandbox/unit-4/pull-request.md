# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/238

**Branch**

fix/14-run-evals-wire-evalsuite

## Eval iterations

**Run history**

1. Only full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. The one miss was `pkg-05` (gold `accept`, my rubric `reject`). I did no `--only` re-runs and no second full run, so this is also the run in `eval-run.txt`.

**Package analysis**

`pkg-05` (source: nushell keybinding merge fix, category clear-accept). Gold label: `accept`. My rubric: `reject`, failing one required check, `Test evidence is decisive`. The package's plan has a test plan with two cases: re-run the issue's script and expect both `atuin` rows, and re-run with two same-name same-key bindings and expect one row plus a warning. The test evidence shows the first case with a full before and after table. For the second case it only says "Same-key redefine prints the one-time warning", with no command or output. My check says the evidence must cover each case the plan's test plan names, or the plan or description must state which one was left out. Neither does, so my rubric failed it. The other six checks passed it (diff inside the plan, description matches the diff, repo checks named with results, no debris, template filled, sensible commits), so this single check decided the verdict. The gold note says: "repro table before/after shown; template sections filled", so the gold label counts the main repro as enough. I did not revise the check to flip it.

**Check rationale**

The check reads, exactly as in the uploaded `tools/pr-precheck/rubric.md`:

| Test evidence is decisive | The test evidence (commands and output), read against the plan's test plan and the repro steps. | Pass if the evidence re-runs the plan's repro on the changed path and shows output that differs before and after the fix, and it covers each failure mode or case the plan's test plan names, or the plan or description states which one was left out and why. Fail if the evidence is only a claim ("tested locally", "works on my machine", "tests pass") with no named command or output, if it exercises a path the fix did not change (a control or unchanged case), if it omits a failure mode the plan's test plan named without saying so, or if an added test does not exercise the fixed behavior. | required |

I wrote it so the pass condition is about the outcome, not the look of the evidence: it needs the plan's repro re-run on the changed path with different before and after output, and it covers every case the plan's test plan names. I took the "every case the plan names" clause from the calibration package `calib-04`, whose gold note rejects a solid fix because it re-ran one of two named failure modes. I rejected the looser version ("some before and after shown is enough"), because the `not-tested` packages (`pkg-04`, `pkg-07`, `pkg-10`, `pkg-14`) are all cases of evidence that never touches the changed path or is only a claim, and that wording would not have separated them from a real repro. I did not revise it after the run.

**Trade-offs**

This check gave up `pkg-05`: it is stricter than the gold label, and a PR that shows its main repro decisively but describes a secondary case in prose gets held. I accept that on purpose, because the same strictness is what holds the `not-tested` packages, which all four were rejected correctly (`not-tested 4/4`). I did not loosen it, so I ran no canaries with `--only`, and nothing else in the run changed. I also know a case it will miss: a PR whose evidence shows before and after output that looks decisive but was produced from a slightly different input than the plan's repro would pass, because the check reads the output against the plan's steps and not against a re-run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
