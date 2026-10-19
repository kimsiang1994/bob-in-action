# Runs, checks and stopping

## Run layout

Every stage reads and writes inside one run folder, `runs/<name>/`:

| Folder | Holds | Written by |
|---|---|---|
| `inputs/` | `complexity-metrics.json` from `tools/metrics/complexity_metrics.py` | the user |
| `data/` | `discovery-evidence.json`, `findings.json`, `plan-tasks.json`, `effort-rows.json` | Bob |
| `reports/` | `assessment.md`, `target-annexes.md`, `implementation-plan.md`, `validation-ledger.md`, `sow.md` (Bob) and `effort-model.csv` (rendered by `validate_effort.py`) | Bob, then the user's checker |
| `checks/` | checker output and the illustrative price table | the user |
| `pilot/` | optional pilot plan, candidate code, review and QA verdict | the pilot personas |

`run_id` in every JSON file is the run name. `source_revision` is the value the user passed to `/discover` (the Git commit of `app/`, if they have one) or the literal `working-tree`; later stages copy it from `data/discovery-evidence.json`. The reports' `kit_version` is `kitVersion` in `.bob/kit-version.json`.

## Stage order

`/discover` then `/assess` then `/plan` then `/price`, each in its persona mode. A stage starts only when the previous stage's files exist. If the user has not said that the previous stage's checker passed, ask once before writing; a failing input makes every later stage fail too. If a needed input is missing, stop and name it; never invent it.

## Writing and stopping

- Read each input once, write your stage's files, then stop and tell the user the exact checker commands for that stage (the root `README.md` lists them). Do not keep re-reading or re-checking files you have written: each extra turn costs Bob credits and adds no evidence.
- When the user pastes a checker failure, correct only what the error names, in place, and stop again. Never repeat an unchanged attempt. If the same error comes back twice, stop and explain it instead of trying again.
- Stop on a native error or limit, a missing input, an unsafe path or an unresolved decision. Leave partial outputs in place for the user to inspect; never delete them.
- Never relax a checker rule to make a file pass, and never edit checker output.
