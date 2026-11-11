---
type: product requirements document
project: PTG (Practice-to-Game Lab)
version: v1.1 (concept widened 2026-11-11: any player who wants to improve but doesn't know what to work on; all parts of the game)
updated: 2026-11-11
supersedes: PRD v0.2 (was 01_blueprint.md §8) and _archive/superseded/PTG-MVP-PRD.md
governed by: 01_foundation/foundation.md (P1–P15, output standard, admission test) · 01_foundation/glossary.md (all terms)
roadmap: 06_roadmap.md
---

# PTG Product Requirements (v1.1)

## 1. Product in one sentence
**PTG is a reasoning mentor for basketball players who want to get better but don't know what to work on. It learns from your own mistakes, teaches the high-level reasoning behind the fix (the read, the why, the how), gives you a way to practice it, checks whether it shows up in your next game, and only calls advice "proven for you" after it holds on a retest. It covers every part of the game: shooting, playmaking, reading plays, defense, rim protection and more.**

## 2. Problem (behavior over time)
| | |
|---|---|
| **Recurring behavior** | A player wants to improve and puts in time, but without guidance they don't know what to work on, why it's going wrong, or whether the work is helping |
| **Over time** | Effort goes up; improvement stays flat or random; the same mistakes repeat in games |
| **Why (current model, HYPOTHESIS)** | Nobody turns their mistakes into a clear priority; they learn *what* good plays look like (KNOW) but not the reasoning to spot them in time (SEE) or the practice to do them under pressure (DO); nothing carries game results back into practice (the open loop) |
| **Evidence** | OBSERVED by the founder (years of solo training without knowing what to fix); UNKNOWN for other players (tested in the pilot) |

## 3. Who it's for
- **Primary (MVP):** an adult recreational or developing player who wants to improve, has no regular coach, plays pickup or league games, and has (or can record) phone film. Training alone is common, but no longer required.
- **Founder as user #1:** every feature runs on the founder first (Phases 0–3).
- **Not in MVP:** players under 13 (COPPA), coaches as buyers, teams.

## 4. Jobs the product does
| ID | Job (player's words) | Moves |
|---|---|---|
| J0 | "Tell me what to work on first, based on my own mistakes." | Priority |
| J1 | "Help me understand the read behind this play." | KNOW → SEE |
| J2 | "Show me the version I can do right now." | SEE → DO |
| J3 | "Tell me honestly what's different in my makes and misses." | DO |
| J4 | "Give me one thing to work on and one thing to look for next game." | DO → GAME |
| J5 | "Tell me what's actually been proven for me." | Memory |

## 5. Business requirements (numbered)
| BR | Requirement | Job | Foundation |
|---|---|---|---|
| **BR-1** | A player can see whether a contest reached the ball before release on their own clips | J3 | P8, P15 |
| **BR-2** | A player can study a play that pauses before the decision, commit an answer, then see the cue | J1 | P3 |
| **BR-3** | Every lesson offers a beginner version of the read and a floor rep that fits the player's space, time and partner | J2 | P6, P14 |
| **BR-4** | Every lesson ends with a game assignment and a ≤10-second game tag | J4 | P4 |
| **BR-5** | Advice is only labeled "proven for you" after a delayed, cue-free retest | J5 | P10 |
| **BR-6** | Every piece of advice shows its evidence grade and what it's based on | all | P1 |
| **BR-7** | The player's video and data stay private by default and can be fully deleted | all | Security |
| **BR-8** | A player gets one priority to work on, chosen from their own logged mistakes, with the reasoning behind it | J0 | P1, P9, P13 |

## 6. Functional requirements (each with a test)
| FR | Requirement | Acceptance test | BR |
|---|---|---|---|
| FR-1.1 | Register a clip with file hash, capture fps, per-frame timestamps, CFR/VFR flag, consent | A VFR test file is flagged VFR; a clip without consent is rejected | BR-1, BR-7 |
| FR-1.2 | Human marks release and contest proxy per event definitions vN; NO/NONE allowed | Two passes are stored separately; nothing is overwritten | BR-1 |
| FR-1.3 | Margin = time(contest proxy) − time(release) from timestamps | CFR fixture: frames ÷ fps matches; VFR fixture: timestamp math used | BR-1 |
| FR-1.4 | Margin shown as a range; "window error" only if the sign holds across it | A sign-flip case shows "can't tell" | BR-1 |
| FR-2.1 | Film lesson pauses before the decision; the player's answer is saved before the reveal | No reveal without a saved answer | BR-2 |
| FR-2.2 | Reveal names the cue and at least one alternative, from the coach library only | Lesson with no coach annotation can't be published | BR-2 |
| FR-2.3 | Each lesson has ≥2 held-out clips | Learning is claimed only if held-out clips are answered correctly | BR-2 |
| FR-3.1 | Beginner version + floor rep with a fade step | Card missing either field fails the claim audit | BR-3 |
| FR-4.1 | Game assignment + game tag (situation · saw it? · acted? · outcome) in ≤10 s | Timed test: tag logged in ≤10 s | BR-4 |
| FR-5.1 | Lesson status `coach's call → testing → held / dropped`; promotion needs a retest event | Promotion without a retest is refused | BR-5 |
| FR-5.2 | A change counts only if bigger than the player's noise band | Inside-band difference reports "inconclusive" | BR-5 |
| FR-6.1 | Every card fills all output-standard fields and passes the claim audit | Missing field or untraced number = card blocked | BR-6 |
| FR-7.1 | Private by default; delete removes raw, derived and generated data | After delete, no record for that player in any store | BR-7 |
| FR-7.2 | Bystanders can be blurred or cropped before storage | Blur step available on every uploaded clip | BR-7 |
| FR-8.1 | Mistake log: each mistake tagged with skill area, situation, what happened, and the evidence (clip, tag or note) | A mistake with no evidence can't drive a priority | BR-8 |
| FR-8.2 | Priority = the mistake pattern that repeats most in games, shown with counts and an evidence grade | Fewer than a set minimum of logged moments → "starting assessment" + targeted questions, never a confident priority | BR-8 |
| FR-8.3 | Each priority explains the reasoning in three layers: the read (what to notice), the why (what the defense or situation gives you), the how (what to do and practice) | Card missing a layer fails the claim audit | BR-8, BR-6 |

## 7. MVP scope
**Vision: every part of the game.** The *method* (mistake → priority → reasoning → practice → game check → retest) is the same for every skill area. What grows skill area by skill area is the *content*: reads, cues, coach-reviewed annotations and evidence cards.

| Skill area | Example mistakes | Content needed | Order |
|---|---|---|---|
| Shooting under a closeout | Rushed release, wrong shot vs. drive choice | Release + contest-proxy marks (rehearsal-01) | **1st** |
| Reading plays / decisions | Missed the open man, late read | Pause-point clips + acceptable reads | 2nd |
| Playmaking | Picked up the dribble, forced passes | Pick-and-roll and drive-and-kick reads | 3rd |
| Off-ball | Stood still, relocated into help | Relocation reads (Invisible Possession) | Later |
| On-ball defense | Beat off the dribble, bit on fakes | Stance/closeout reads, defender archetypes | Later |
| Help defense + rim protection / blocking | Late rotation, fouling on contests | Rotation reads, verticality cues | Later |

**Why one area first (systems reason):** each area needs its own validated content library and coach review. Opening all at once would spread a small review capacity across every area, so none gets proven. The first area proves the method; the rest reuse it.

**In (built in this order, by hand first):**
1. **Feature 01, Practice-to-Game** (closeout situation): BR-1, BR-4, BR-5, BR-6
2. **Feature 05, One Cue**: BR-5, BR-6 (no video needed)
3. **Feature 02, Read Before React** on the player's own film: BR-2, BR-3

**Out (non-goals):** minors · automatic shot detection · joint-angle scoring · NBA broadcast clips without rights · AI making decisions at runtime · 90-day plans · social features · payments · Features 03 (Opponent Fingerprint) and 04 (Invisible Possession)

## 8. What PTG never claims
Why a specific shot missed · that a drill transfers before a game check shows it · any number it didn't measure or calculate · what a pro was thinking · medical or injury advice

## 9. Architecture constraints (summary; details in other docs)
- Code computes; the AI only explains structured results (P12) → `03_skills-architecture.md`
- Append-only event log; everything else derived → `02_system-design.md` §1
- Time from per-frame timestamps, never frames ÷ fps → `05_cv-methods.md` §4
- Pose/CV only for features that pass the hand-mark gate (G2) → `04_cv-blueprint.md`
- License must fit the IP plan (AGPL vs. Apache) before any model is chosen

### Who checks the advice (decided 2026-11-11)
- **For now: evidence grades + the founder.** Every card shows its grade and passes the claim audit; then the founder reviews it before a player sees it.
- **Honest label:** the founder is an experienced player, not a certified coach, so founder-reviewed advice is shown as grade **C ("coach's call")** until a player's own retest makes it **P**.
- **Capacity rule:** the founder reviews only cards that change what a player does next. Review hours per week are logged.
- **Revisit when** review takes more than the founder's weekly cap, or the pilot grows past 5 players. Options then: a paid coach-review tier, a coach partner per skill area, or peer review.

## 10. Success metrics
| Metric | Type | Healthy (prediction) | Source |
|---|---|---|---|
| Held-out read accuracy | SEE | Rises after lessons | FR-2.3 |
| Game-tag "saw it" rate on the target read | GAME | Rises slowly over weeks | FR-4.1 |
| Lessons held per player | Stock | Slow climb; fast = filter too loose | FR-5.1 |
| Cards passing the claim audit first try | Quality | High and stable | FR-6.1 |
| Founder's prediction accuracy | Learning | Rises | Judgment log |

## 11. Risks
| Risk | Signal | Response |
|---|---|---|
| Solo rep can't contain the defender cue | SEE improves on clips, not in games | Game tags are the real test; pilot measures it |
| Players don't log game tags | Tag rate < 50% | Shorten the tag; remind after games |
| Coach-review capacity (the founder) | Review queue grows | Review only outputs that change what a player does |
| Measurement doesn't repeat | Rehearsal fails G1 | Fix camera/definitions before anything else |
| Copycat drift | A feature only matches competitors | Anti-copycat checklist (`06_roadmap.md` §2) |

## 12. Appendices (concepts carried over from the alphaXiv sessions)

### A. Race Clock acceptance tests (write the expected result before building)
Model: arrival = 0 if d ≤ r; no arrival if v = 0; else (d − r) ÷ v. Margin = arrival − release.
Hand-checkable fixture: d = 15 ft, r = 3 ft, v = 12 ft/s, release 0.8 s → arrival **1.0 s**, margin **+0.2 s**.

| Test | Required behavior |
|---|---|
| Speed increases, all else fixed | Arrival decreases (when starting outside the boundary) |
| Speed = 0, defender outside boundary | "No arrival" |
| Defender starts inside boundary | Arrival = 0 |
| Self-rated balance changes | Timing unchanged |
| Visual cone changes | Visibility label may change; timing unchanged |
| An input is missing | Field-specific error; never silently 0 |
| Demonstration values | Visibly labeled as demo |
| Any output | Never shows shooting % or "transfer likelihood" |

**Claim tests too:** does any output imply causation? present an assumption as a measurement? claim a solo cue equals a defender? imply faster is always better? ignore the player's stated space and equipment?

### B. Practice profile (purpose-aware, deferred feature)
Ask about each drill the player already does:
1. What action is practiced?
2. Is the action decided before the rep?
3. What information changes during the rep?
4. Is a defender present?
5. Is there a time constraint?
6. What changes between reps?
7. **What is the drill for?** (A comfortable-mechanics drill isn't "bad" for lacking a defender.)

Output: known conditions, **unknown conditions (never turned into "absent")**, differences from the target game situation, one suggested experiment, why it was chosen, what it can't test.

The 5-dimension representativeness rubric from the brainstorm (information coupling · decision · variability · time pressure · load, 0–2 each) is kept only as an **experimental rubric, grade C**. It is never summed into a scientific-looking percentage or shown as a goal (P2).

### C. When an LLM may be added (and how)
Only for a narrow, tested reason, e.g., "players struggle to describe practice in structured fields, so the LLM drafts a record for their review." When added:
- calculations stay outside the model · structured outputs only · unknowns preserved
- extracted records shown for approval · only reviewed evidence relevant to the task is supplied
- user notes and retrieved text are data, not instructions · log context, output, model and validation
- fall back to templates when output is invalid or unavailable · API keys on a backend, never in the browser
- a source link in generated text isn't enough; check the source actually supports the claim

### D. Coaching voice: generic vs. PTG
| Generic | PTG |
|---|---|
| "Practice catch-and-shoot threes." | "Your target shots come after relocation; your routine starts stationary, so this adds movement before the catch." |
| "Shoot faster." | "Your reviewed clips show longer catch-to-release under closeouts. Work on preparation before the catch, tracking makes and comfort, not speed alone." |
| "Train like Curry." | "Use a documented principle from elite preparation, adapted to your level, space and capacity." |
| "You need more confidence." | "Your practice removes the shoot-or-drive choice. Add a controlled decision before progressing to live closeouts." |

Each observation has three layers: **observed difference** → **possible significance** (not a cause) → **direct next step**.

## 13. Open questions
1. Is catch-vs-closeout the right first situation for the founder's own game?
2. When were empty-gym, no-decision reps the right thing, and what signaled moving on? (defines the target practice mix)
3. Who is the second marker for test-retest?
4. Does the founder have pickup film yet?
