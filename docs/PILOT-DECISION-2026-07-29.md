# PILOT DECISION — 2026-07-29

## Verdict

**Overall decision: ADAPT**

The bounded Autoresearch pattern is worth reusing, but not as the stock
unbounded loop and not with single-run keep/discard decisions. On Wednesday,
July 29, 2026, the pilot stayed within the hard cap, produced useful evidence,
and showed that the pattern's core value is the **bounded evaluate -> mutate ->
measure -> keep/revert loop**. The ML training result also showed that
single-run wins can collapse under stricter validation, so any reuse beyond
this repository needs stronger evaluators and significance gating from the
start.

## Scope And Bounds

- Date: Wednesday, July 29, 2026
- Branches used:
  - `pi/2026-07-29-research-autoresearch-pilot`
  - `autoresearch/jul29-pilot`
- Global cap:
  - 8 total experiment runs
  - 90 minutes total experiment wall-clock
- Actual usage:
  - 8 total runs
  - 2,467.9 seconds total wall-clock
  - 41.1 minutes total wall-clock
- Remote mutations: none
- `prepare.py` rerun: no
- File mutation discipline:
  - hardened `program.md` first
  - experiment changes limited to `train.py`
  - run ledger recorded in `results.tsv`

## Run Ledger

| Run | Commit | Result | Memory GB | Status | Note |
|---:|---|---:|---:|---|---|
| 1 | `2603270` | 1.620487 | 8.4 | keep | fresh pilot baseline |
| 2 | `c38a16b` | 1.566742 | 8.4 | keep | halve total batch size to `2^15` |
| 3 | `b67e26d` | 1.495913 | 6.9 | keep | remove final logit cap |
| 4 | `aa4be04` | 1.461336 | 6.9 | keep | extend `WARMDOWN_RATIO` to `0.75` |
| 5 | `1636a63` | 1.500074 | 6.9 | discard | lower `MATRIX_LR` to `0.03` |
| 6 | `bc96963` | 1.567052 | 6.9 | discard | keep a `5%` final LR floor |
| 7-8 | `train.py` hash `1826a32` | 1.637052, 1.662734 | 6.9 | validation | `rigor.py` 2-seed validation of final best config |

## Best Search Result Vs Validation

Best single-run search result:

- commit `aa4be04`
- `val_bpb 1.461336`
- `peak_vram_mb 7042.6`

`rigor.py` validation output captured on Wednesday, July 29, 2026:

```text
seed 2/2: val_bpb 1.662734
BASELINE 1826a32: 1.649893 +/- 0.012841 (2 seeds)
```

Interpretation:

- The bounded search found a materially better **single-run** result than the
  baseline.
- That improvement did **not** survive immediate multi-run validation.
- The validated mean for the final best config, `1.649893`, was worse than the
  fresh pilot baseline, `1.620487`.
- This is strong evidence that the stock single-run keep/revert loop is too
  noisy to trust on its own, even in the repository where the pattern
  originated.

## Operational Observations

- The bounded protocol worked. The pilot stopped on schedule and produced a
  usable record instead of open-ended experimentation.
- Repository-only mutation discipline was workable. The only runtime friction
  was local git-signing behavior, which required one unsigned local commit path
  for discard bookkeeping.
- Memory pressure was **not** the limiting factor on Wednesday, July 29, 2026.
  The observed peak stayed around `6.9-8.4 GB`, well below the README's
  example `~27 GB`, so concurrent machine use was not the main constraint.
- Validation discipline was the real constraint. The search loop can find
  plausible local minima quickly, but without significance gating it happily
  promotes noise.

## Decision By Target Use Case

### 1. Personal-skill optimization

**Decision: ADAPT**

Reason:

- This is still the strongest next use case because the artifact under mutation
  is small and explicit, such as one `SKILL.md`.
- The pilot shows the pattern is useful only when the evaluator is bounded and
  the stopping rule is explicit.
- The main lesson to carry forward is: do not accept single-run wins. Use a
  fixed evaluation set, regression checks, and repeated scoring before keeping
  a skill change.

Required adaptation:

- deterministic or near-deterministic task set
- explicit regression suite
- token-cost and latency metrics alongside quality
- confidence-gated keep/discard from the first run

### 2. Piece-of-work classification

**Decision: ADAPT**

Reason:

- This may be the cleanest near-term adaptation because the evaluator can be a
  finite human-labeled set with accuracy and consistency metrics.
- Compared with ML training, the score surface should be easier to stabilize.
- The bounded loop is appropriate here, but only with a frozen labeled corpus
  and a confusion-matrix style review of failures.

Required adaptation:

- frozen labeled benchmark set
- taxonomy-consistency checks
- repeated scoring or cross-validation
- mandatory rollback on regression, not one-off spot wins

### 3. Future Hermes retrieval

**Decision: PARK**

Reason:

- The pilot supports the pattern in principle, but Hermes retrieval is not yet
  ready for execution because the prerequisite evaluation corpus still does not
  exist.
- `docs/USE_CASES.md` already names the missing ingredients: representative
  corpus, realistic questions, answer or relevance labels, citation metrics,
  latency metrics, and cost metrics.
- Without that harness, the loop would optimize anecdotes rather than quality.

Condition to un-park:

- 30-100 representative retrieval questions
- expected answers or relevance labels
- citation coverage metric
- latency and cost budget
- bounded protocol modeled on this pilot, with validation from day one

## What This Pilot Changed

The main decision is not "the ML config improved." The main decision is:

- keep the bounded protocol
- reject unattended infinite-loop execution
- require explicit caps, explicit rollback rules, and explicit summaries
- require stronger validation than one-run wins before promoting the pattern to
  other domains

## Final Recommendation

Promote the pattern as a **bounded experimental method**, not as an
"autonomous forever" agent.

Next action order:

1. Build a fixed evaluation harness for `piece-of-work-classifier`.
2. Re-run the pattern there with confidence-gated acceptance from the start.
3. Treat personal-skill optimization as the next adaptation after that harness
   proves stable.
4. Leave Hermes retrieval parked until the labeled evaluation corpus exists.
