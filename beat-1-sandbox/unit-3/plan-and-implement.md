# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

---

## Posted upstream

**GitHub username**

Jugal-JG

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/14#issuecomment-6328749005

Plan for #14, built on my repro above (f89c06f, Windows 11, Python 3.11.9, `LLM_PROVIDER=mock`).

**Cause.** `scripts/run_evals.py` never calls `EvalSuite`: `main()` prints two lines and returns, so `make eval` exits 0 and `eval_results.json` is never created (`test -f` returns 1 before and after). The controls rule out the other suspects: `EvalSuite().run(...)` called returns scores directly, `basic_profile.json` parses fine, and `grep -rn EvalSuite` finds only its own class definition. The only defect is that nothing wires them together.

**Change (one script, one test file).** In `scripts/run_evals.py`, load every JSON under `tests/fixtures/sample_profiles/`, score each with the existing `EvalSuite`, and write `eval_results.json` (per-profile scores plus the mean). Exit non-zero with a stderr message if no fixtures are found or one can't be read, so an unmeasured run can't look green. New tests in `tests/unit/test_run_evals.py`.

**Not in this change:** an actionability scorer, a mock-LLM seam in `ReviewGenerator`, edits to the scorers, and `eval.yml`'s silent `catch`. This answers the two questions from my claim by deferring both. Nothing from a maintainer yet, so I'm taking the narrow reading.

**Test.** Re-run my repro: `test -f eval_results.json` goes from `1` to `0` after `python scripts/run_evals.py`, and the file holds three scores in 0.0-1.0. An empty fixtures dir now exits non-zero.

**Unknowns.** Because the seam doesn't exist, I'll use a fixed reference review built from the fixture, not generated feedback, so these scores check the wiring, not review quality. I'll say that in the PR. I haven't run this on Ubuntu/CI.

---

## Your branch

**Branch**

fix/14-run-evals-wire-evalsuite

**Evidence**

Unit 2 repro steps (issue #14, `scripts/run_evals.py`), before and after. Before is the script from `main` at `f89c06f`; after is the script on the branch. Run from the repo root with the venv's Python.

```
=== BEFORE (script from main @ f89c06f)
$ test -f eval_results.json; echo $?
1
$ python scripts/run_evals.py; echo $?
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
0
$ test -f eval_results.json; echo $?
1
=== AFTER (branch fix/14-run-evals-wire-evalsuite)
$ test -f eval_results.json; echo $?
1
$ python scripts/run_evals.py; echo $?
Running RAG evaluation suite...
2026-10-21 18:44:16 [info     ] relevance_scored               avg_score=0.14285714285714285 chunks_count=2 query_len=7
2026-10-21 18:44:16 [info     ] faithfulness_checked           claims_count=2 score=1.0 supported_count=2
2026-10-21 18:44:16 [info     ] eval_suite_complete            faithfulness=1.0 overall=0.5714285714285714 relevance=0.14285714285714285
Scored 1 profile(s). Results written to C:\Users\jugal\Desktop\Projects\AI-301\pathreview-ai301-fa26-s1\eval_results.json
0
$ test -f eval_results.json; echo $?
0
$ cat eval_results.json
{
  "profiles": [
    {
      "profile": "basic_profile",
      "relevance_score": 0.14285714285714285,
      "faithfulness_score": 1.0,
      "overall_score": 0.5714285714285714
    }
  ],
  "mean": {
    "relevance_score": 0.14285714285714285,
    "faithfulness_score": 1.0,
    "overall_score": 0.5714285714285714
  }
}
$ empty fixtures dir
Evaluation failed: no benchmark profiles found in \tmp\emptyfx
Running RAG evaluation suite...
exit=1
```

New unit tests: `.venv/Scripts/python.exe -m pytest tests/unit/test_run_evals.py -q` gave `3 passed`, and `ruff check` and `ruff format --check` were clean.

## Eval iterations

**Run history**

1. Only run: first full run, `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. Before it I did one `--only pkg-02` smoke run (1/1) to check the harness worked on Windows. That run is not scored and the harness refuses to save it. The last score matches the agreement line in `eval-run.txt`.

**Package analysis**

`pkg-04` (source: junegunn/fzf#4260, category thread-convention). Gold: `reject`. My rubric: `reject`. The plan is docs-only: a man-page note, README examples and an FAQ entry telling people to add `> /dev/tty`. The thread highlights show the owner already did more than that: "This seems to be the culprit", pointing at `src/tui/light_windows.go` lines 70-84, then posting a patched test binary and asking the reporter to test it. The plan comment mentions the repro and the `/dev/tty` control but never mentions the owner's finding or the patched binary. My `Comment follows the thread and repo asks` check failed it, because the comment picks a different route (documenting a workaround) and never acknowledges the maintainer's direction. The other required checks passed it: the plan is bounded, executable and has a decisive test, so the thread check alone decided the verdict.

**Check rationale**

The check reads, exactly as it is in the uploaded `rubric.md`:

| Comment follows the thread and repo asks | The plan comment, read against the thread highlights (maintainer statements, proposed fixes, requests for testing, prior PRs) and the repo-facts block's bug-report template and contribution lines. | Pass if the comment's approach follows, or explicitly explains why it diverges from, every explicit direction a maintainer or owner gave in the thread highlights (a named culprit, a proposed fix, a rejected approach, a request to test something, an open PR on the same fix), and does not contradict the repo-facts block's stated asks. Pass by default when the thread highlights hold no maintainer direction. Fail if the plan or comment picks a different route (for example documenting a workaround when the owner already isolated the code culprit and asked for testing) and never acknowledges the maintainer's direction. | required |

I did not revise this check; it went into the first full run as written, and that run agreed on both thread-convention packages (pkg-04 and pkg-20). I wrote it to judge the outcome, not the comment's shape. A comment that merely mentions a maintainer does not pass: the check lists what counts as direction (a named culprit, a proposed fix, a rejected approach, a request to test, an open PR) and allows a different route only when the comment explains it. I rejected the alternative of folding AI disclosure into this same row, and kept it as its own check, so a plan that follows the thread but omits a required disclosure (pkg-20) fails on a check with that name.

**Trade-offs**

This check passes by default when the thread has no maintainer direction. I saw that on my own issue: in live mode plan-check graded it `pass` because #14 has no maintainer reply, even though two classmates' plans overlap. So it will not catch a comment that ignores a classmate's competing plan; I cover that with the voice guide rule, which does not change the verdict. I did not need canaries or an `--only` re-run, because the first full run was 20/20 and nothing was revised afterwards, so there was no loosened check that could flip an agreeing package. A second case it will miss: a comment that technically mentions the maintainer's direction in one line but then plans something else would pass the "acknowledges" test.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in `tools/plan-check/`.
