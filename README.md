# Bob in action: an SI assessment you can run yourself

This repository lets an SI or reseller partner watch IBM Bob do assessment work on a legacy application, step by step, in their own copy of Bob. Four slash commands take Bob from discovery to a nonbinding draft statement of work:

| Command | Bob's job | What Bob writes under `runs/<name>/` |
|---|---|---|
| `/discover` | Read the legacy app and record evidence with exact file-and-line pointers | `data/discovery-evidence.json` |
| `/assess` | Turn the evidence into findings, an assessment and one annex per migration target | `data/findings.json`, `reports/assessment.md`, `reports/target-annexes.md` |
| `/plan` | Plan dependency-ordered work packages, staffing and a validation ledger | `data/plan-tasks.json`, `reports/implementation-plan.md`, `reports/validation-ledger.md` |
| `/price` | Author effort rows and a draft SOW; the SI's own tools then calculate an illustrative price | `data/effort-rows.json`, `reports/sow.md` |

After each command you run a checker yourself. Bob has no shell in this kit, so every check is a plain Python command you can read and repeat.

Everything here is synthetic: the application, the customers, the roster, the rate card, the tickets and the timesheets. Nothing Bob writes is a quote or an approval.

## What is in the repository

| Folder | What it holds |
|---|---|
| `app/` | The legacy application Bob assesses: a deliberately imperfect Java 8 invoice desk. Read-only. See `app/README.md`. |
| `.bob/` | The Bob kit: the four commands, nine persona modes, rules, skills, hooks and MCP wiring. See `.bob/README.md`. |
| `tools/` | Four read-only mock MCP servers, the complexity-metrics tool, the price checkers and the SI commercial engine. See `tools/README.md`. |
| `targets/` | Worked examples of the invoice-preview feature migrated to seven targets, for reference. |
| `runs/` | Created by you; one folder per run, holding everything Bob and the checkers write. |

## Prerequisites

- **IBM Bob**, signed in, with this repository's root opened as a trusted folder (Bob loads the workspace hooks only for a trusted folder).
- **Python 3.11 or later** on the `PATH` as `python`. The hooks, the MCP servers and every checker use only the standard library. Check with `python --version`. If your machine only has the `py` launcher, change `python` to `py` in `.bob/settings.json` and `.bob/mcp.json`.
- Optional: **Java 8 (JDK) and Maven**, only if you want to build and run the legacy app. The Bob commands do not need it.

## Run it

1. Open the repository root in Bob and start a new task. Bob should show the line **"SI kit hooks active"**. If it does not, the hooks are not running (usually Python is missing or the folder is not trusted); fix that before going on, because the guard that keeps Bob's writes inside `runs/` is one of those hooks.
2. Pick a run name, for example `acme-1` (letters, digits, dots, dashes and underscores, starting with a letter or digit).
3. Run each stage in its mode, then its checker, from the repository root. Each stage reads the files the previous one wrote; Bob asks once whether you ran the previous checker.

**Discover** (mode `si-architect`):

```
/mode si-architect
/discover acme-1
```

```
python .bob/skills/si-discover/scripts/normalize_evidence.py --input runs/acme-1/data/discovery-evidence.json --source-root .
python tools/metrics/complexity_metrics.py --out runs/acme-1/inputs/complexity-metrics.json
```

`/discover` takes an optional second argument, the source revision it records (for example a Git commit); without it the run records `working-tree`.

**Assess** (still `si-architect`):

```
/assess acme-1
```

```
python .bob/skills/si-assess/scripts/validate_findings.py --input runs/acme-1/data/findings.json --run-id acme-1 --evidence-map runs/acme-1/data/discovery-evidence.json
python .bob/skills/si-collate/scripts/validate_report_quality.py --partial-stage assess --pack runs/acme-1/reports
```

**Plan** (mode `si-pm`):

```
/mode si-pm
/plan acme-1
```

```
python .bob/skills/si-plan/scripts/validate_dag.py --input runs/acme-1/data/plan-tasks.json --run-id acme-1 --findings runs/acme-1/data/findings.json
python .bob/skills/si-collate/scripts/validate_report_quality.py --partial-stage plan --pack runs/acme-1/reports --plan-tasks runs/acme-1/data/plan-tasks.json
```

**Price** (mode `si-commercial`):

```
/mode si-commercial
/price acme-1
```

```
python .bob/skills/si-business/scripts/validate_effort.py --input runs/acme-1/data/effort-rows.json --run-id acme-1 --plan-tasks runs/acme-1/data/plan-tasks.json --csv-output runs/acme-1/reports/effort-model.csv
python tools/checkers/illustrative_price.py --csv runs/acme-1/reports/effort-model.csv --output runs/acme-1/checks/illustrative-price.json
python tools/checkers/check_effort_numbers.py --csv runs/acme-1/reports/effort-model.csv --sow runs/acme-1/reports/sow.md --price-table runs/acme-1/checks/illustrative-price.json --output runs/acme-1/checks/effort-check.json
python .bob/skills/si-collate/scripts/validate_report_quality.py --pack runs/acme-1/reports --rubric .bob/standards/report-quality-rubric.json --findings runs/acme-1/data/findings.json --plan-tasks runs/acme-1/data/plan-tasks.json --effort-rows runs/acme-1/data/effort-rows.json
```

Exit code 0 means a check passed. When a checker prints errors, paste them into Bob in the same mode and ask it to correct only what they name. The tools that write files refuse to overwrite, so delete `effort-model.csv`, `illustrative-price.json` and `effort-check.json` before running the price checks again. The final report check is strict: a first pass that fails on a wording rule or two is normal, not a broken kit.

The finished pack is `runs/acme-1/reports/`: the assessment, the target annexes, the implementation plan, the validation ledger, the effort model and the draft SOW. The illustrative price table in `runs/acme-1/checks/` is synthetic, marked `not_a_quote`, and comes from the SI's tools, not from Bob.

## What it costs

Each command uses Bob credits from your own account; the checkers cost nothing. In our own runs of these four stages, Bob used roughly:

| Stage | Bob credits |
|---|---|
| `/discover` | about 0.4 to 0.5 |
| `/assess` | about 3 |
| `/plan` | about 3.2 |
| `/price` | about 2.5 to 3.3 |
| All four | about 9 to 10 |

Those runs were controlled, with fixed limits on how long each stage could go. Interactive use can cost more: every correction round after a checker error is another Bob turn, and long, open-ended chats in a stage add up. Treat the figures as a guide, not a promise.

## What keeps Bob in its lane

- **Modes.** Each persona mode may edit only its own stage's files under `runs/<name>/`; the orchestrator, collator and tech-lead modes cannot edit at all.
- **The guard hook.** Before every tool call, `.bob/hooks/pre_tool_guard.py` allows reads only inside this workspace, writes only inside `runs/<name>/` (no `..`, no links that lead elsewhere, never `app/`, `tools/`, `targets/` or `.bob/`), refuses every shell command and every delete, move or rename, and allows only the ten read-only tools of the four mock MCP servers.
- **The checkers.** Bob's output is accepted only when your checkers say so. They never relax a rule to let a draft through.

These are backstops for honest mistakes in a demo, not a security sandbox.

Not yet tested in a live Bob session: we have checked the hooks by giving them tool calls directly, but have not yet watched Bob run them. The "SI kit hooks active" line in step 1 of [Run it](#run-it) is how you check they are running in your Bob.

## Migration targets

`targets/` holds Bob's worked examples of one feature, invoice preview, moved off the legacy app. Each is a bounded proof, not a full migration, and each folder's `README.md` says how to build and test it.

| Folder | Target |
|---|---|
| `targets/java/` | Java 8 to Java 21, strangler-style: a Java 21 preview service the legacy app calls |
| `targets/kotlin/` | Kotlin preview server |
| `targets/dotnet/` | ASP.NET Core minimal API |
| `targets/python/` | Python FastAPI service |
| `targets/typescript/` | TypeScript browser client for preview |
| `targets/postgresql/` | H2 to PostgreSQL for the preview reference data |
| `targets/microservices/` | Preview pulled out into its own Spring Boot service |

To build `targets/microservices/`, use a Java 21 JDK: on Java 25 it did not compile when we tried it.

The assessment's target annexes discuss the same seven targets.

## The governed version

This repository is the interactive path: you drive Bob and run the checks. Internally, Ingram also runs the same four stages through a governed controller that starts each Bob session in a frozen copy of the workspace, enforces a credit budget per stage, runs every checker itself and accepts or rejects each stage automatically, keeping a record of what was spent and why.
