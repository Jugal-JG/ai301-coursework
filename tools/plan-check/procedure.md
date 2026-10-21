# Procedure: how this skill grades a plan package

## Read order

1. Read the repo-facts block first. Write down three things: the bug-report template asks, the contribution-policy line, and whether that line explicitly requires disclosing AI use (tool and/or extent). Write "no explicit disclosure requirement" if it is silent, permissive, or only says the human must review the work.
2. Read the issue and the thread highlights second. List every explicit maintainer or owner direction: a named culprit or file, a proposed fix, a rejected approach, a request to test something, an open PR on the same fix. Mark each with who said it.
3. Read the repro-evidence block third, and read it closely. Write down: the numbered steps, the artifact (error text, trace, output), and every control run (same steps with one thing changed). For each control, write what it rules in and rules out. This is the ground truth the plan is held against.
4. Read the candidate plan fourth. Mark its stated cause, its in-scope and not-in-scope lines, its files or areas, its approach steps, its test plan, and its risks. Do this after the evidence so you judge the plan against the repro, not the reverse.
5. Read the candidate plan comment last. Compare it to the plan and to your list from step 2.

## Evidence gathering

1. Diagnosis: copy the plan's cause sentence. Then, for each control and artifact noted in Read order step 3, write one line: "consistent" or "contradicts" the cause, with the quote that decides it.
2. Scope: list each approach step. For each, write "needed for the repro's failure" or "extra", and name the extra thing (migration, rewrite, new option, framework, harness change, cleanup). Copy the not-in-scope line, or write "none stated".
3. Executability: list the files, functions or components the plan names, and the chosen approach. Write "none named" or "decision deferred" where that is true.
4. Test plan: copy the sentence that names the outcome the test observes. Write whether it is tied to a repro step or artifact, or generic.
5. Thread and repo asks: for each maintainer direction from Read order step 2, find the sentence in the plan or comment that follows it or explains a divergence from it. Write "engaged" or "ignored".
6. Disclosure: search the plan comment for any statement that AI assistance was used. Write the sentence or "no disclosure".
7. Unknowns: copy the plan's risk or unknown sentences, and note any sentence claiming certainty the repro does not show.
8. Comment-to-plan match: write whether the comment's cause, scope and test equal the plan's, and note any promise the plan does not contain.

## Check execution

1. Run the checks in the rubric's table order, each using only the notes from Evidence gathering. Do not re-read the whole package for a check unless its notes are empty.
2. Grade `pass` only when the rubric's pass condition is met by a fact you can quote. Grade `fail` when a fail condition in the rubric is met. Quote that fact as the evidence line.
3. If the evidence for a check is absent from the package (for example the plan has no test plan at all), grade `fail` for the check and say what is missing. Grade `unclear` only when the package has the evidence but it can be read two ways and the rubric's condition does not decide it. State both readings.
4. Judge the thing itself, not its shape. Length, headings and confident tone never change a grade. A short plan can pass every check and a long polished plan can fail one.
5. Where this procedure is silent on something, say so in the summary rather than inventing a step.

## Verdict assembly

1. List every check with its grade. Mark each `required` or `preferred` as the rubric says.
2. Apply the rubric's verdict rule: `accept` only if every required check is `pass`. Any required `fail` or `unclear` gives `reject`. Preferred checks are listed but never change the verdict.
3. In the summary, quote the single deciding fact for the verdict: for a `reject`, the quote behind the first failing required check; for an `accept`, the one-line reason the weakest required check still passed.
4. Emit the JSON block last, with one entry per check (name, grade, one-line evidence) and the verdict. Nothing follows the JSON.
