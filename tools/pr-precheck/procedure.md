# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts first (eval: the repo-facts block; live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`). Write down: every section or checklist item the PR template asks for, any file the policy says a PR must carry (a changelog entry, a test), the commands the repo names as checks, the commit convention if one is stated, and whether the contribution policy explicitly requires disclosing AI use. Write "no explicit disclosure requirement" if it is silent, permissive, or only makes the human responsible for review.
2. Read the issue and the thread highlights second. Note any maintainer direction the PR should respect.
3. Read the plan third (eval: the plan-context block; live: `plan.md`). Write down four lists: the files and areas it names, the approach steps it promises, the not-in-scope items, and every deviation note. Write down its test plan: each repro step and each failure mode it says it will re-run, with the output it expects. Read the plan before the diff so you judge the diff against what was promised and not the reverse.
4. Read the diff fourth (eval: the `### Diff` block; live: `git diff main...HEAD`, with `git log main..HEAD --oneline` for commits). List every changed file and, for each file, every hunk in one line. Mark each hunk as added behavior, test, comment, debug line, commented-out code, dead code, or formatting-only.
5. Read the test evidence fifth (eval: `### Test evidence`; live: `test_evidence.md`). Write down each command run and what its output showed.
6. Read the description last (eval: `### Description` and `### Title`; live: `pr_draft.md`). Write down every claim it makes about what changed, what was tested, and what was left out. Read it last so you compare its claims against the diff and the evidence you already hold.

## Evidence gathering

1. Plan fidelity: for each changed file in your diff list, write "in plan", "covered by deviation note" or "not in plan". Find the plan's not-in-scope items in the diff and write "present" or "absent" for each. For each promised approach step, write "in diff", "deferred (quote the note)" or "missing".
2. Description against diff: for each claim from Read order step 6, find the hunk or file that backs it and write "backed" or "not in diff". Compare the Changes list with your hunk list and note any hunk the description omits.
3. Test evidence against test plan: for each repro step or failure mode in the plan's test plan, find the command and output in the evidence and write "shown before and after", "shown after only", "exercises an unchanged path", "claimed without output", or "absent". Check that an added test, if any, exercises the fixed behavior.
4. Repo checks: for each check command from Read order step 1, find it in the evidence and write its result, or "not shown" and whether the description says why.
5. Debris: from your hunk marks, count debug lines, commented-out code, dead functions or unused imports, and formatting-only hunks. Quote each one. Note the commit messages and whether they are placeholders.
6. Standards: for each template section or item from Read order step 1, write "filled with real content" or "empty or ignored". Check whether the diff carries any file the policy requires. Search the description for a statement that AI was used and write the sentence or "no disclosure".

## Check execution

1. Run the checks in the rubric's table order, each using only its notes from Evidence gathering. Re-read the package for a check only if its notes are empty.
2. Grade `pass` only when the rubric's pass condition is met by a fact you can quote. Grade `fail` when any fail condition in the rubric is met, and quote the hunk, claim or missing item.
3. If the evidence for a check is absent from the package (for example no test evidence at all), grade `fail` and say what is missing. Grade `unclear` only when the package holds the evidence but it can be read two ways and the rubric does not decide it. State both readings.
4. Grade the artifact, not its shape. Length, headings, a ticked checklist and a confident tone never change a grade. A short PR can pass every check and a polished one can fail one.
5. Treat a shortfall that the plan's deviation notes or the description state plainly, with a reason, as disclosed: it does not fail the plan-fidelity or test checks.
6. Where this procedure is silent, say so in the summary rather than inventing a step.

## Verdict assembly

1. List every check with its grade and its weight (`required` or `preferred`, as the rubric says).
2. Apply the rubric's verdict rule: `accept` only if every required check is `pass`. Any required `fail` or `unclear` gives `reject`. Preferred checks are listed and never change the verdict.
3. In the summary, quote the single deciding fact. For `reject`, quote the evidence behind the first failing required check. For `accept`, state in one line why the weakest required check still passed.
4. In live mode, add the voice-guide notes to the summary (not to the verdict).
5. Emit the JSON block last, with one entry per check (name, grade, one-line evidence) and the verdict. Nothing follows it.
