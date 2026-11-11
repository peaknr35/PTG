# 04_experiments: real runs

One job: hold every real experiment, one folder per run. **Product.** New every run, never edited after the debrief (corrections go in a new note).

## How a run works
1. Copy `../_templates/rehearsal/` → `04_experiments/<run-name>/`
2. Fill `predictions.md` **before** anything else (the judgment log)
3. Follow the run's `README.md`: capture → pass 1 → (24 h) → pass 2 → `debrief.md`
4. Debrief decision → revise event definitions → freeze the next version

## Status (derived from files)
| File state | Means |
|---|---|
| `predictions.md` blank | Not started |
| `ledger.csv` P1 columns filled | Pass 1 done |
| `ledger.csv` P2 columns filled | Pass 2 done |
| `debrief.md` filled | Run complete |

## Run index (one line per run)
| Run | Purpose | Status |
|---|---|---|
| `rehearsal-01` | Fix event definitions; check CFR/VFR; test mark repeatability | Kit ready, not started |
| `session-2026-11-10_audit` | Retrospective of the whole ideation session: timeline, research pipeline, 25-item AI catch log, Claude's own mistakes, scorecard, next steps | Done (`audit.md`) |

## Load rules
- Load one run's folder. Don't load other runs unless comparing them.
- Event definitions live in each run (`event-definitions-vN.md`). The measurement method lives in `03_design/04_cv-blueprint.md` App. A and `03_design/05_cv-methods.md` §4, §6.

## Human check
After each run: are the predictions compared honestly in `debrief.md`? Is the decision box ticked?
