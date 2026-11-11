# 03_design: the plans, in reading order

One job: hold PTG's plans and specs. **Versioned docs, not run outputs.** No code lives here.

## Reading order
| # | File | Read it when | Status |
|---|---|---|---|
| 01 | `01_blueprint.md` | First: the concept, competitors, how PTG stands out, curriculum, architecture, security, IP, decision log (PRD, roadmap and to-do moved to 06/07) | Current |
| 02 | `02_system-design.md` | How data flows and stays correct (DDIA), the AI delivery cycle (APIV, 14 steps), case matrix, requirement ledger | Current |
| 03 | `03_skills-architecture.md` | Shared skills, feature agents, who controls what, when a step becomes a skill (§6b) | Current |
| 04 | `04_cv-blueprint.md` | Computer vision: measurement catalog, YOLO11/26 tables, sensor PRD, Kinovea workflows, **Appendix A rehearsal ledger** | Current |
| 05 | `05_cv-methods.md` | VFR timing, RLE, multi-camera sync, joint angles, labeling QC protocol | Current |
| 06 | `06_roadmap.md` | Plan, anti-copycat checklist, gated roadmap, business track, 14-day to-do | **Current. The one home for roadmap and to-do** |
| 07 | `07_prd.md` | PRD v1.0: problem, users, jobs, BR-1–7, FR with tests, MVP scope, metrics, risks | **Current. The one home for requirements** |
| — | `boards/` | Mock-ups only (see `boards/CONTEXT.md`) | Reference |
| — | `_archive/superseded/PTG-MVP-PRD.md` | Only for history; replaced by `07_prd.md` | Superseded |

## Load rules
- Load **one** design doc for the task. Most tasks need only one section of it.
- Do NOT load all seven. Do NOT treat anything here as evidence: claims trace to `02_research/library.md`.

## Human check
A change here: does it follow `01_foundation/foundation.md` (admission test §5)? Does it use glossary terms? Is the decision logged in `01_blueprint.md` §11?
