---
type: workspace context
updated: 2026-11-11
---

# PTG: what it is, how it fits together, where it stands

## What PTG is
- **The problem (lived, widened 2026-11-11):** players who want to get better but don't know what to work on without guidance. The founder lived it: years of solo practice and film study, no clear idea what to fix, and games didn't change. You could *know* a play, but not *see* it in time or *do* it under pressure.
- **Scope:** every part of the game (shooting, playmaking, reading plays, off-ball, defense, rim protection). The method is shared; content grows one skill area at a time, starting with shooting under a closeout (`03_design/07_prd.md` §7).
- **The model:** KNOW → SEE → DO → GAME (`01_foundation/foundation.md` §1).
- **The role (revised 2026-11-11):** a **real product for players**, built by hand first. This replaces the 2026-11-10 "learning lab + portfolio" decision (`01_foundation/goal-system-map.md` §5). Who pays is UNKNOWN.
- **Build order (decided 2026-11-11):** 1. Film: see what matters (own clips) → 2. Before practice: one focus → later: after-game log, weekly priority.

## How the shelves connect
```
01_foundation (rules) ──governs──► everything
02_research (evidence) ──grades──► 01_foundation principles ──shape──► 03_design (plans)
03_design (plans) ──define──► 04_experiments (real runs) ──results──► judgment log + library + foundation revisions
```
- **Factory** (stable, changes only by reviewed revision): `01_foundation/`, `02_research/`
- **Plans** (versioned): `03_design/`
- **Product** (new every run): `04_experiments/`

## Current phase
**Phase A0, measurement rehearsal (no code).**
- Ready: `04_experiments/rehearsal-01/` (the kit is prepped, nothing marked yet)
- The founder already has game and practice film (stated 2026-11-11), so no recording is needed to start.
- Next human action: fill `04_experiments/rehearsal-01/predictions.md`, then pick 5 existing clips (2 or 3 game closeout shots, the rest practice) and copy them into the run folder
- Open question for the founder: who pays for PTG, and for what? (`01_foundation/goal-system-map.md` §5)

- Roadmap + to-do: `03_design/06_roadmap.md` · Requirements: `03_design/07_prd.md` · Concept site: `05_portfolio/ptg-concept.html`

## Status (derive it, don't trust this line)
Look inside `04_experiments/*/`: empty `predictions.md` = not started · filled `ledger.csv` P1 columns = pass 1 done · filled `debrief.md` = run complete.

## Known gaps
- ⚠ `s40279-023-01884-3.pdf` (Sports Medicine 2023, representative design) is cited but **missing**. Re-download into `02_research/papers/01_skill-science/`.
- Not created yet on purpose: `features/`, `skills/`, `ledger/`. They get created when there's real work for them (`03_design/03_skills-architecture.md` §6b).
