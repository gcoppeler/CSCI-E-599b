# Standardized equivalence evaluation prompt

You are an independent evaluator. You didn't write either codebase and don't trust either one's claims about itself, including any claims of equivalence in its README.

BASELINE (control): {BASELINE_GIT_URL}

TEST GROUP (AI-assisted): {CANDIDATE_GIT_URL}

QUESTION: Is the test group functionally equivalent to the baseline, so both can be evaluated under the same framework as comparable implementations?

DEFINITION: Functionally equivalent means the candidate does the same job on standard inputs and produces statistically close output. It does not mean byte-identical behavior on every edge case. The candidate is EQUIVALENT if at least 95% of standard inputs match under the comparison mode below, with no systematic difference in meaning.

STEP 0: ESTABLISH THE SETUP (decide these yourself and state each choice with a reason). 
1. Clone both repos and record the commit hash of each. All results refer to those commits.
2. Read the baseline's README, docs, and public API, and write down what the library is for and the core use cases a typical user relies on. This defines "the same job."
3. Choose the comparison mode from what the library outputs: exact (deterministic output with one correct answer, e.g., parsed config values); semantic (deterministic output where formatting can vary without changing meaning, e.g., XML or JSON text; normalize before comparing, and note formatting-only differences but count them as matches); distributional (nondeterministic output, e.g., random data generators; compare formats, validity, and distributions with an appropriate statistical test across a fixed set of seeds).
4. Build a corpus of standard inputs: at least 1,000 real-world inputs matching the core use cases, stratified by type so no single source dominates. Prefer real files available in your environment or from public package data over inputs you invent; use generated inputs only to fill gaps, and say which is which. Use default options plus the commonly documented ones.

STEP 1:
1. Install both in clean, separate environments and confirm the public APIs line up (names, parameters, defaults). Note anything missing or renamed.
2. Run the corpus through both. Report match rates per core use case and per operation, both exact and under the comparison mode. Bucket every mismatch by cause and give a minimal reproducing example for each bucket.
3. If the baseline has a test suite, run it against the candidate, changing only import paths. Report it as a secondary signal; passing tests alone does not establish equivalence.
4. Probe edge cases: non-default options, malformed input, unusual types, error types, CLI behavior and exit codes. Report divergences as a compatibility-risk list ranked by how likely a real user is to hit them. These do not decide the verdict.
5. Flag stubs, hardcoded values, or logic that only works on the happy path, since those would make corpus agreement misleading.

OUTPUT: 
1. Verdict: EQUIVALENT or NOT EQUIVALENT, with the match rates that decide it.
2. Setup: commits tested, comparison mode, and corpus composition, each with its reason.
3. Table of core use cases: pass/fail, match rate, evidence.
4. Mismatch buckets with counts and minimal examples.
5. Compatibility-risk list.
6. Anything you couldn't verify. If you didn't run it, don't claim it.
