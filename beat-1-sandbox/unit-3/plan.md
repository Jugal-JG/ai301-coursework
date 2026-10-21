# Plan: issue #14, make `scripts/run_evals.py` actually run `EvalSuite`

## Diagnosis

`scripts/run_evals.py` never calls the scorer. `main()` prints two lines and
returns; the "TODO: Implement eval runner" comment is the whole body, and the
final `print("Evaluation complete. Results written to eval_results.json")`
claims a file that nothing writes. So `make eval` and the `RAG Evaluation` CI
job succeed while measuring nothing.

This follows from my unit 2 repro, not from the issue text alone. The evidence
I rely on (commit `f89c06f`, Windows 11, Python 3.11.9 venv, `LLM_PROVIDER=mock`):

```
$ test -f eval_results.json; echo $?
1
$ .venv/Scripts/python.exe scripts/run_evals.py; echo $?
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
0
$ test -f eval_results.json; echo $?
1

$ grep -rn "EvalSuite" --include="*.py" .
./rag/evaluator/eval_suite.py:22:class EvalSuite:
```

The controls rule out the other suspects. The scorer works when called directly
(`EvalSuite().run('python fastapi', [{'text': 'python expert building fastapi
services'}], 'This developer knows Python and FastAPI.')` returned
`EvalResult(relevance_score=1.0, faithfulness_score=1.0, overall_score=1.0)`),
and `tests/fixtures/sample_profiles/basic_profile.json` parses as a dict with 4
keys. So the scorer and the fixture are fine and the only defect is that the
runner never connects them. The runner also exits 0 with no output file, which
is why CI looks green. I read, but did not run, `.github/workflows/eval.yml`:
its `catch (e) {}` turns a missing `eval_results.json` into the string
`'Eval results not found.'`.

## Scope

**In scope (one change, one file plus its test):**

- `scripts/run_evals.py`: load every `*.json` under
  `tests/fixtures/sample_profiles/`, score each with the existing `EvalSuite`,
  and write `eval_results.json` (per-profile scores plus the mean). Exit
  non-zero, with a message on stderr, if no fixtures are found or a fixture
  cannot be read, so a measurement that did not happen can no longer look like
  success.
- `tests/unit/test_run_evals.py`: new tests for that behavior.

**Not in scope** (each deferred on purpose):

- An actionability scorer. `rag/evaluator/` has none, and adding one is a new
  feature that belongs in its own issue.
- A mock-LLM injection seam in `ReviewGenerator` (it builds its own
  `openai.OpenAI` client in `__init__`). Without the seam the runner cannot
  drive generation, so it will not run the full RAG pipeline; see the unknowns.
- Any change to `RelevanceScorer`, `FaithfulnessChecker` or `EvalSuite`.
- `.github/workflows/eval.yml`. The silent `catch` is a separate hardening
  change. With my change the script step itself fails before the comment step
  runs.
- More benchmark fixtures, and the failing `vector-db` container.

## Files to touch

1. `scripts/run_evals.py` (edit)
2. `tests/unit/test_run_evals.py` (new)

## Approach

1. Add `load_profiles(fixtures_dir) -> list[tuple[str, dict]]`: sorted
   `*.json` files; raise `FileNotFoundError` if the directory has none.
2. Add `build_inputs(profile) -> (query, chunks, feedback)` using only the
   fixture fields: query from each repo's `language` and `topics`; one chunk
   per repo (`{"text": description + readme_content}`); feedback is a fixed
   reference review assembled from each repo's `name` and `description`. This
   is a deliberate stand-in for generated feedback (see unknowns).
3. Add `score_profile(name, profile) -> dict` calling `EvalSuite().run(...)`
   and returning the three scores.
4. Rewrite `main()` to loop over profiles, write `eval_results.json` as
   `{"profiles": [...], "mean": {...}}`, print the real path and counts, and
   return a non-zero exit code on any failure (`sys.exit(main())`).
5. Write the tests (below), run `make lint` / `ruff` and the unit suite.

## Test plan

Re-run my unit 2 repro steps after the fix and compare:

| Step | Before (unit 2) | Expected after |
|---|---|---|
| `test -f eval_results.json; echo $?` before running | `1` | `1` (delete it first) |
| `.venv/Scripts/python.exe scripts/run_evals.py; echo $?` | prints "complete", exit `0`, no file | prints the number of profiles scored and the output path, exit `0` |
| `test -f eval_results.json; echo $?` after running | `1` | `0` |
| `python -c "import json;print(json.load(open('eval_results.json'))['mean'])"` | file missing | three numeric scores between 0.0 and 1.0 |
| run with an empty fixtures dir (new check) | n/a | non-zero exit and a message on stderr, no "complete" line |

Unit tests in `tests/unit/test_run_evals.py`: (a) one fixture in a temp dir
produces `eval_results.json` whose `profiles` has one entry with
`relevance_score`, `faithfulness_score` and `overall_score`; (b) an empty
fixtures dir returns non-zero and writes no file; (c) the real
`basic_profile.json` loads and scores. The pre-fix script fails (a) and (c),
which is the "before" for this check.

## Risks and unknowns

- **The score will not measure generated review quality.** I have not tested
  how the mock provider could drive `ReviewGenerator`, because the seam does not
  exist yet. The stand-in feedback makes faithfulness near-trivial. I will say
  so in the PR and in the script docstring rather than present the numbers as
  review quality. If a maintainer wants generation in this issue, I will stop
  and ask before adding the seam.
- **Overlap with another plan.** aishani-muk posted a plan on this issue
  (`feat/14-offline-eval-runner`) that adds retrieval and puts the logic under
  `rag/`. I have not run their branch. Mine is deliberately smaller and
  stays in the script. If maintainers prefer theirs, I will rebase onto it or
  take only the failure-exit and test parts.
- **Windows path handling** is only verified on my machine; CI runs on Ubuntu
  and I have not run it there.

## Deviations

Nothing material changed; the plan held. Three small differences from what I wrote, none of which changes scope or the posted comment:

- `main()` takes `fixtures_dir` and `output_path` arguments (defaulting to the real paths) and returns an exit code, so the tests can point it at a temp dir. The plan only said it would return a non-zero code.
- The failure branch also catches `ValueError`, which covers a fixture that is not valid JSON. The plan only named "cannot be read".
- The runner's output includes the existing `EvalSuite` structlog lines. I left them alone because logging in the scorers is out of scope.

I re-ran plan-check on the plan before building and got accept. I did not add a follow-up comment on the issue, since the posted plan is still true.
