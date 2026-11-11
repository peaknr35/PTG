# PTG: Practice-to-Game Lab

A product in the making (decided 2026-11-11), built by hand first: a reasoning mentor for basketball players who want to improve but don't know what to work on. It turns their own mistakes into one priority, teaches the reasoning behind the fix, and checks it in games. Covers every part of the game; content grows one skill area at a time. Also where the founder practices systems thinking, system design and AI delivery.

## Where things live
| Folder | Holds |
|---|---|
| `CONTEXT.md` | What PTG is, the current phase, status, how the shelves connect |
| `01_foundation/` | The rules: principles, shared vocabulary, the founder's goal |
| `02_research/` | Evidence: graded library, papers by topic, tools, clippings |
| `03_design/` | Plans and specs, in reading order; `boards/` = mock-ups |
| `04_experiments/` | Real runs (rehearsals, sessions), one folder each |
| `05_portfolio/` | Shareable pieces (concept site, case studies). A view, never the source |
| `_templates/` | Blank folders to copy when starting a new run |
| `_archive/` | Duplicates, superseded docs, raw chat transcripts. Don't load |

## Route by task
| If you need to… | Go to | Then stop at |
|---|---|---|
| Understand PTG cold | `CONTEXT.md` | its "Current phase" section |
| Check a rule, term or claim standard | `01_foundation/CONTEXT.md` | the one file it names |
| Find or add evidence | `02_research/CONTEXT.md` | `library.md` card |
| Read or change a plan | `03_design/CONTEXT.md` | the doc it names |
| See the roadmap or to-do | `03_design/06_roadmap.md` | §3 or §5 |
| Check requirements | `03_design/07_prd.md` | the BR/FR row |
| Run or review an experiment | `04_experiments/CONTEXT.md` | that run's `README.md` |
| Start a new run | copy `_templates/rehearsal/` → `04_experiments/<run-name>/` | fill `predictions.md` first |
| Report status | scan `04_experiments/*/` (which files are filled) + `CONTEXT.md` | — |

## Rules every agent follows
1. **Use only the words in `01_foundation/glossary.md`.** New term → add it there first.
2. **Every claim gets a label** (OBSERVED / INFERRED / HYPOTHESIS / UNKNOWN) and an evidence grade (`01_foundation/foundation.md`).
3. **Code computes, the model explains.** Never invent numbers, thresholds or study results.
4. **Load only the folder the task needs.** Never load `_archive/` or whole PDFs unless asked.
5. **Never overwrite raw data or a human's marks.** Corrections are new versions.
6. **Nothing moves to the next step until a person has read the last output.**

## Naming
- Folders: `NN_name/` = order matters. `_name/` = system folder (not a step).
- Runs: `rehearsal-NN`, `session-YYYY-MM-DD`.
- Papers: readable names; renamed ones start with the year.

## Working mode
Default is **thinking first** (systems tutor): ask one question at a time, separate observation from interpretation, and no new features or docs unless the founder asks to build.
