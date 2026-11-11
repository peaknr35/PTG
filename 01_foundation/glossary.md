---
type: shared language (ontology), the ONE place PTG terms are defined
project: PTG (Practice-to-Game Lab)
version: v0.2
updated: 2026-11-11
changes: v0.2 (2026-11-11) records the Coaching terms added with the concept widening (skill area, mistake (logged), reasoning, starting assessment). No meaning changed.
rule: every other file and every prompt to an AI uses these words exactly. A new term gets added here first. A changed meaning gets a new version + a note in "Retired terms."
---

# PTG Glossary

## The core model
| Term | Means | Not to be confused with |
|---|---|---|
| **KNOW** | Understanding what a good play is | SEE (spotting it in time) |
| **SEE** | Spotting the cue in real time, before acting | KNOW |
| **DO** | Executing the action under pressure | Gym skill (execution without pressure) |
| **GAME** | A real game or pickup run: the only proof of transfer | A live drill |
| **Transfer** | A skill trained in practice showing up in GAME or on held-out clips | Retention (same task, later) |
| **Retention** | The skill still there on a later retest without the cue | Transfer |

## Video and measurement
| Term | Means |
|---|---|
| **Clip** | One original video file of one rep |
| **Rep** | One attempt: catch → release → landing |
| **Condition** | `solo` · `simulated` (a cue or timer, no defender) · `live` (a real defender). Never pooled |
| **Catch** | The hand–ball contact moment on the pass. **Operational definition lives in the current `event-definitions-vN.md` of the run** (one home per fact) |
| **Release** | The moment the ball leaves the fingers. **Operational definition: current `event-definitions-vN.md`** |
| **Contest proxy** | An image-based proxy for the defender's hand reaching the ball, not a 3D "contest." Replaces the retired term "defender arrival." **Operational definition: current `event-definitions-vN.md`** |
| **Cue** | The start signal in a `simulated` rep (beep, "go"). Never a contest |
| **NO** | Not observable: the event can't be seen reliably. An honest answer, not a missing value |
| **NONE** | The event didn't happen (e.g., the hand never reached the ring before release) |
| **Margin** | time(contest proxy) − time(release). Positive = released first. Not a judgment of the decision |
| **Window error** | A miss where the contest proxy came before release, and the sign holds across the estimate range (P15) |
| **Execution error** | A miss where there was time (positive margin, robust sign) and it still missed |
| **PTS** | Presentation timestamp: each frame's own time in the file. The only source of time for intervals |
| **CFR / VFR** | Constant / variable frame rate. VFR means frames ÷ fps is wrong |
| **Pass (P1/P2)** | One complete marking of a clip set by one annotator |
| **Adjudicated** | The final mark after a disagreement is resolved |
| **Quality code** | `BLUR` · `OCCL` · `OUT` · `AMBIG` · `LIGHT` · `VFR` · `OTHER` |
| **Ground truth** | Human marks that passed the agreement gate (G1). Not "the truth." The best available reference |

## Data
| Term | Means |
|---|---|
| **Event (data)** | One append-only fact in the log (e.g., `EventMarked`, `LessonPromoted`). Different from a basketball "event" like release. Say "log event" when ambiguous |
| **System of record** | The append-only event log: original facts, never edited |
| **Derived** | Anything recomputed from the log (ledger views, stats, lesson memory) |
| **Generated** | Text produced by a model or template from derived data (mentor cards) |
| **Raw → reviewed → derived → generated** | The four record kinds, in order; never overwrite an earlier kind |
| **Definition version** | The version of the event definitions a mark used (v0, v1…) |
| **Granularity** | The level a value lives at: **frame** (keypoints, PTS), **rep** (catch, release, margin), **session** (agreement stats, % unknown), **player** (lessons). A value never gets stored at the wrong level (e.g., margin is per rep, not per frame) |
| **Pixel vs. normalized coordinates** | Pixel = position in image pixels. Normalized = divided by width/height (0–1). Angles and distances are computed in pixels or calibrated units, never normalized |

## Coaching
| Term | Means |
|---|---|
| **Skill area** | A part of the game PTG covers: shooting · reading plays · playmaking · off-ball · on-ball defense · help defense / rim protection. The method is shared; each area has its own content |
| **Mistake (logged)** | One game or practice moment that went wrong, tagged with skill area, situation, what happened and its evidence (clip, tag or note). The raw material for a priority |
| **Reasoning (read · why · how)** | The three layers every priority explains: what to notice, what the situation gives you, what to do and practice |
| **Starting assessment** | What PTG gives when there aren't enough logged moments yet: questions and a tentative focus, never a confident priority |
| **Read** | The information a player should notice before acting in a situation |
| **Beginner version** | A simplified form of a pro read that fits the player's current level |
| **Floor rep** | A solo practice rep built to rehearse a read |
| **Game assignment** | What to look for in the next GAME |
| **Game tag** | A ≤10-second log after a GAME: situation, did I see it, did I act, outcome |
| **Priority** | The single thing the mentor is working on; held until its gate |
| **Mentor card** | The output standard (foundation §4); every field required |
| **Lesson** | A claim about what works for this player |
| **Lesson status** | `coach's call` → `testing` → `held (P)` or `dropped` |
| **Evidence grade** | A · B · C · P (foundation §2) |

## Quality and process
| Term | Means |
|---|---|
| **Gate** | A pass/fail check that unlocks the next step (G0–G5 and phase gates) |
| **Gold set / hidden gold** | Pre-agreed answers used to qualify and monitor annotators |
| **Kill switch** | Automatically turning off a feature whose validation fails |
| **Board** | A throwaway design mock-up (03_design/boards/v1-invented-formulas.html, 03_design/boards/v2-honest-model.html). Never shipped as code |
| **BR / FR** | Business requirement / functional requirement (requirement ledger) |
| **Judgment log** | Your predictions vs. what happened |

## Retired terms (don't use)
| Retired | Use instead | Why |
|---|---|---|
| defender arrival / "within reach" | **contest proxy** | Implied a 3D judgment from 2D video |
| affordance expired / affordance decay | **window error** (+ P15) | Sounded measured; it's a labeled hypothesis |
| transfer likelihood | (nothing) | Was an invented formula |
| representativeness % (as a goal) | drill purpose + practice profile | A score became the goal |
| diagnosis | observation / hypothesis | PTG doesn't diagnose |
