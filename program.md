# autoresearch-mlx bounded pilot program

This program replaces the stock autonomous loop for the **2026-07-29 pilot**.
The purpose of this run is **not** to optimize ML training indefinitely. The
purpose is to test whether the Autoresearch pattern is safe and useful enough
to adapt into other domains.

## Pilot objective

Produce bounded evidence for a go / park / adapt decision about reusing the
Autoresearch pattern beyond ML training, especially for the ranked use cases in
`docs/USE_CASES.md`.

Required final artifact:

- `docs/PILOT-DECISION-2026-07-29.md`

## Non-negotiable bounds

The pilot must terminate itself when **either** limit is reached:

- **8 total experiment runs**, including the baseline.
- **90 minutes total experiment wall-clock**, measured from the start of the
  baseline run to the end of the final allowed run.

Additional hard limits:

- Kill any single run that exceeds **15 minutes** wall-clock and log it as
  `crash`.
- Do not re-run `prepare.py`.
- Do not add dependencies or modify `pyproject.toml`.
- Do not modify `prepare.py`.
- Do not modify any file outside this repository.
- Allowed external reads are limited to `~/.cache/autoresearch/`.
- Do not push, fetch, pull, or otherwise mutate any remote.
- Do not run unattended. This pilot is **supervised only**.

The old "loop forever", "never stop", and sleep-through-the-night autonomy are
explicitly out of scope for this pilot.

## File permissions

Writable files are restricted to this repository, with the normal experiment
target narrowed further as follows:

- `train.py` is the **only** file experiment ideas may modify.
- `results.tsv` records every run.
- `run.log` captures stdout/stderr for each training run.
- `docs/PILOT-DECISION-2026-07-29.md` records the verdict.
- `program.md` may be updated only to maintain this bounded protocol.

Everything else is read-only for the experiment campaign.

## Required sequencing

Execute the pilot in this order:

1. Harden this program and commit it on the pilot branch.
2. Cut a child branch named `autoresearch/<tag>` from the pilot branch.
3. Establish a fresh baseline on current hardware.
4. Run **3 supervised experiments** against that baseline.
5. Use the **remaining global budget** for **1 bounded campaign**.
6. Validate finalists with `rigor.py`.
7. Write the decision record and stop.

If the global budget expires at any point, stop immediately and write the
decision record from the available evidence.

## Git discipline

- Start from the pilot branch `pi/2026-07-29-research-autoresearch-pilot`.
- Create exactly one campaign branch for the run: `autoresearch/<tag>`.
- Make one explicit commit per experiment change to `train.py`.
- Stage explicit paths only. Never use `git add -A`.
- Keep a winning experiment by staying on its commit.
- Revert a losing experiment by returning to the last kept commit.
- Do not rewrite published history. Nothing in this pilot should be pushed.

Recommended explicit staging pattern:

```bash
git add train.py
git commit -m "experiment: <description>"
```

For run logging:

```bash
git add results.tsv docs/PILOT-DECISION-2026-07-29.md
git commit -m "docs: record bounded pilot decision"
```

## Baseline and run protocol

The baseline is a real experiment run on the current hardware. Do not rely on
historical numbers already present in `results.tsv`.

Every run must follow this protocol:

1. Record the current kept commit and cumulative budget state.
2. If this is not the baseline, edit `train.py` only.
3. Commit the proposed `train.py` change before running.
4. Launch training with redirected output:

```bash
uv run train.py > run.log 2>&1
```

5. Never stream training output into the agent context. Read results afterward
   with grep:

```bash
grep "^val_bpb:\|^peak_vram_mb:" run.log
```

6. If the grep output is empty, inspect the crash with:

```bash
tail -n 50 run.log
```

7. Log the run to `results.tsv` using tab-separated columns:

```text
commit	val_bpb	memory_gb	status	description
```

8. Decide keep / discard / crash:
   - `keep`: lower `val_bpb` than the current kept baseline.
   - `discard`: equal or worse `val_bpb`.
   - `crash`: no valid metric or forced timeout.

9. If discarded or crashed, return to the last kept commit before the next idea.

## Campaign shape for this pilot

This pilot is intentionally small. Treat the total run budget as:

- Run 1: baseline
- Runs 2-4: supervised experiments
- Runs 5-8: one bounded campaign using whatever wall-clock remains

The bounded campaign is still subject to the same keep/revert discipline and
must stop early if the 90-minute limit is reached before all four slots are
used.

## Evaluation standard

Primary metric:

- `val_bpb` from `prepare.py` / `evaluate_bpb`

Secondary observations:

- peak memory from `peak_vram_mb`
- compile / eval overhead implied by total runtime
- operational friction: crashes, reversions, noise, supervision burden

Simplicity remains a tie-breaker. Tiny metric wins that add obvious complexity
should be treated skeptically in the decision record.

## Finalist validation

Before writing the decision record, validate the finalists with `rigor.py`.

- Read `rigor.py` before using it.
- Use it to capture a significance-gated view of the best candidate(s).
- Include the relevant `rigor.py` output in `docs/PILOT-DECISION-2026-07-29.md`.

`rigor.py` informs the final verdict; it does not override the hard global cap.
If the cap is nearly exhausted, spend the remaining budget on the most useful
validation available and then stop.

## Required summary and stop condition

The pilot must end with a written summary in
`docs/PILOT-DECISION-2026-07-29.md` that includes:

- the exact number of runs completed
- total wall-clock consumed
- the best kept result and any validated finalist result
- whether memory pressure constrained the search
- a go / park / adapt decision for:
  - personal-skill optimization
  - piece-of-work classification
  - future Hermes retrieval

Once the summary is written, the pilot is over. Do not continue exploring.
