---
type: skills + feature-agent architecture (design only; no SKILL.md files created yet)
project: PTG (Practice-to-Game Lab)
status: draft v0.1
updated: 2026-11-10
builds on: 02_research/frameworks.md (ICM+) · 03_design/02_system-design.md (event log) · 01_foundation/foundation.md · 01_foundation/glossary.md
rule: scaffold these folders only after rehearsal-01 (ICM playbook: build what the work proves it needs)
---

# PTG Skills and Feature Agents

## 0. The shape in one picture

```
              ┌──────────── LIBRARIES (read-only to every agent) ────────────┐
              │ foundation · glossary · evidence cards · COACH LIBRARY        │
              └──────────────────────────────┬────────────────────────────────┘
                                             │
  FEATURE AGENTS (one per idea)  ──uses──►  SHARED SKILLS (one job each)  ──writes──►  EVENT LOG
  01 practice-to-game                       DERIVE · INFER · HUMAN                    (append-only)
  02 read-before-react                                                                    │
  03 opponent-fingerprint            ◄──────────── JUDGE (claim-audit + coach gate) ◄─────┘
  04 invisible-possession
  05 one-cue
```

**Three ideas hold it together:**
1. **The five ideas are one loop** (observe → hypothesize → experiment → re-measure), so they **share skills** instead of each having its own brain.
2. **Every skill is typed** DERIVE (code), INFER (LLM) or HUMAN (you or a coach). Only DERIVE and HUMAN can create numbers or facts.
3. **No skill judges its own work.** A separate judge (claim-audit script + a coach gate) decides whether output moves on (Humanize, evaluator-optimizer).

---

## 1. Who owns what (the control table)

| Output | Owner | Agents may |
|---|---|---|
| Timestamps, CFR/VFR, file hashes | DERIVE (clip-intake) | Read |
| Moment marks (catch, release, contest proxy, first step, pickup) | HUMAN (CV only after gate G2) | Read; never edit |
| Condition tags | HUMAN (assisted) | Read; suggest |
| Numbers (margins, %, noise bands, retest results) | DERIVE | Read; quote with evidence IDs |
| Acceptable reads, cues, defender archetypes, responses | HUMAN (coach library) | Quote; never write |
| Hypotheses, experiment drafts, questions, explanations | INFER | Write as `PROPOSED`, each claim linked to evidence IDs |
| Status changes (`APPROVED`, `HELD`, `DROPPED`) | HUMAN (coach gate) | Never |

---

## 2. The shared skills (10, split by job, not by basketball topic)

### DERIVE (scripts: deterministic, testable)
| Skill | One job | Writes (log events) | Must not |
|---|---|---|---|
| **clip-intake** | Register a clip: hash, capture fps, PTS list, CFR/VFR, context (practice/game), consent ID | `ClipRecorded`, `TimestampsExtracted` | Accept a clip without consent or with a re-encoded copy |
| **stats-and-noise** | Compute margins (from PTS), makes/attempts, % unknown, the player's noise band | `MeasureComputed` | Report a difference inside the noise band as real (P9) |
| **retest-check** | Compare a delayed, cue-free retest with baseline → **retention** result | `RetestResultLogged` | Call it "transfer" (glossary: retention ≠ transfer) |
| **transfer-check** | Compare game tags / game clips with baseline → **transfer** result, with sample sizes | `TransferResultLogged` | Report from fewer than a preset number of game moments; use the word "verdict" |
| **claim-audit** (the judge) | Check every INFER output: labels present, numbers traced to DERIVE events, glossary terms only, no forbidden phrases | `AuditPassed` / `AuditFailed(reason)` | Be the same model that wrote the output (if it's an LLM step, use a different model) |

### HUMAN (you, a second rater, a coach; tools assist)
| Skill | One job | Writes | Must not |
|---|---|---|---|
| **moment-marking** | Mark frames per event-definitions vN; two passes; NO/NONE allowed | `EventMarked(pass, annotator, def_version)` | Mark with the outcome visible (blind where possible) |
| **condition-tagging** | Tag each rep: solo / simulated / live; stationary / moving; predictable / unpredictable; drill purpose | `ConditionTagged` | Pool conditions |
| **coach-gate** (filled by the founder for now; see `07_prd.md` §9) | Approve, reject or edit proposals; set status | `ProposalApproved/Rejected`, `LessonPromoted/Dropped` | Approve without the evidence IDs attached |

### INFER (LLM: proposals only)
| Skill | One job | Writes | Must not |
|---|---|---|---|
| **hypothesis-framing** | One testable claim from a difference between conditions + ≥1 competing explanation + a pass/fail test | `HypothesisProposed` | Diagnose ("your problem is…"); invent numbers; set APPROVED |
| **experiment-design** | Fill a **coach-approved template**: ABBA blocks, reps, randomization, fade step, retest date, stop rules | `ExperimentProposed` | Create a new drill outside the template library |
| **socratic-dialogue** | Ask a short set of questions; record **noticed / chose / could execute** separately | `DialogueLogged` | Grade the player's read itself (the coach library defines acceptable reads) |
| **card-writer** | Turn approved, structured results into the mentor card (all fields) | `CardDrafted` | Add any claim not in the inputs; skip a card field |

### Each SKILL.md uses the same sections (ICM+ format)
```
# <skill> — one job: <sentence>
Type:        DERIVE | INFER | HUMAN
Inputs:      exact paths / log events it may read
Do NOT load: …
Steps:       numbered, each tagged DERIVE or INFER
Output:      log event(s) + file, with schema
Must not:    (the most important section)
Judge:       who checks it (claim-audit / coach-gate / second rater)
Loop:        trigger · stop condition · state file · budget · escalate to
On failure:  TRACE tag → which context file, CREATE or UPDATE
```

---

## 3. How control is actually enforced (honest version)

**Folders don't enforce anything.** "A stage that doesn't list a skill can't use it" is a *request* in markdown (ICM playbook: guarantees belong in code or permissions). The ladder:

| Stage of PTG | Enforcement |
|---|---|
| **Now** (manual, you + an AI chat) | Each feature's `CONTEXT.md` lists allowed skills; you check at the gate |
| **Agent-built prototype** | An **allowlist file** per feature + hooks that block reads and writes outside it; the claim-audit script runs automatically on every INFER output |
| **Product** | Permissions in code: INFER steps have **no write access** to the measurement tables; only the coach role can change status |

---

## 4. The five features, composed

| Feature agent | Skills | Controls | Coach gate | Needs first |
|---|---|---|---|---|
| **01 Practice-to-Game** (attacking a closeout) | clip-intake, moment-marking, condition-tagging, stats-and-noise, hypothesis-framing, experiment-design, retest-check, transfer-check, claim-audit, card-writer | The practice-vs-game comparison and the experiment cycle | Approves the hypothesis and the experiment; promotes lessons | Rehearsal-01 + frozen definitions v1 |
| **02 Read Before React** | clip-intake, moment-marking (freeze points), socratic-dialogue, stats-and-noise (held-out accuracy), claim-audit | The freeze-and-ask flow; noticed / chose / could execute | **Writes** the acceptable reads per clip | 30–50 permissioned, coach-annotated clips (own film first) |
| **03 Opponent Fingerprint** | condition-tagging (archetype), hypothesis-framing, experiment-design, transfer-check, claim-audit | The defender profile and its revisions after matchups | Writes the archetypes and responses | Archetype library (doesn't exist yet) |
| **04 Invisible Possession** | clip-intake, moment-marking (off-ball), socratic-dialogue, claim-audit | Annotated relocation sequences + alternatives | Approves every annotation; no "would have scored" | Multi-player identification (manual only for now) |
| **05 One Cue** | condition-tagging (cue A/B), experiment-design, stats-and-noise, retest-check, claim-audit, card-writer | The cue A/B log and the "did it stick" result | Approves cue wording | Nothing new (no video needed) |

**The combination worth protecting:** running **02** (SEE) and **01** (DO) on the **same clip** separates "didn't see it" from "saw it, couldn't do it." That's KNOW/SEE/DO made into product, and it's the strongest differentiator in the set.

### Feature 01's pipeline with gates
```
clip-intake → moment-marking (2 passes) → condition-tagging → stats-and-noise
   → hypothesis-framing → claim-audit → COACH GATE
   → experiment-design → claim-audit → COACH GATE → player runs it
   → stats-and-noise → retest-check (retention) → transfer-check (game) → claim-audit
   → COACH GATE (promote / hold / drop lesson) → card-writer → claim-audit → player
                ▲                                                            │
                └──────────────── next cycle (priority held until gate) ◄────┘
```

---

## 5. What I changed from the pasted proposal (and why)

| Pasted version | Change | Why |
|---|---|---|
| 7 skills, no judge | **+ claim-audit** as a separate judge | The writer is a weak judge of its own output (Humanize) |
| Skills untyped | Every skill and step typed **DERIVE / INFER / HUMAN** | Only code and humans make numbers (P12, Workflow as Knowledge) |
| clip-ledger records "frame rate" | **clip-intake** records **PTS + CFR/VFR + hash** | Frames ÷ fps is wrong on VFR files |
| transfer-check covers the One Cue retest | Split into **retest-check** (retention) and **transfer-check** (game) | They're different tests (glossary) |
| "transfer **verdict**" | "transfer **result**, with sample size" | "Verdict" overclaims from small samples |
| "defender closed within [margin]" in the template | Use glossary terms: **contest proxy**, **margin** | Ontology drift in the very first template |
| No numbers/stats skill | **+ stats-and-noise** | Every "difference" needs a noise band (P9) |
| No output skill | **+ card-writer**, audited | The mentor card standard needs one owner |
| "A stage that doesn't list a skill can't use it" | Enforcement ladder (§3) | Markdown is a request, not a lock |
| Coach approves everything | Flagged as the **#1 capacity limit** (§6) | The coach gate is a B loop that caps growth |

---

## 6. Systems check

- **Bottleneck (B loop):** every feature runs through the **coach gate**, and right now the coach is you. Coach hours per week cap how many cycles PTG can run. Design for it: batch approvals, and only gate the INFER outputs that change what a player does.
- **Leverage:** the shared skills are the R2 "freedom loop" from your goal map. Build `claim-audit`, `stats-and-noise` and `clip-intake` well once, and every feature and every **client project** reuses them. These three are also your strongest portfolio pieces.
- **Trap:** designing all five features now is KNOW without DO. Feature 01 exercises 10 of 10 skills, so building it builds the platform.

## 6b. Foundation first? Only the parts that are expensive to change
**Build properly from day one** (changing them later breaks every feature):
1. **The ledger / event log**: clip IDs, PTS, conditions, consent, marks (rehearsal-01's `ledger.csv` is version 0)
2. **The ownership rules**: §1 control table
3. **The contract template**: §2 SKILL.md sections

**Everything else gets extracted from a working feature.** Steps start *inside* feature 01's folder. A step graduates to `/skills` only when it passes the **promotion test**:
- needed by **2+ features**, **or** run on **≥10 real clips with no contract change**
- and it has a judge and a "must not" section

(Same idea as the classic "rule of three" in software: don't abstract until the pattern has actually repeated.)

**Why, in systems terms:** a skill built before its contract is stable creates a hidden stock of **rework**. Every definition change (like "defender arrival" → "contest proxy") multiplies across every skill built on it. Extracting after reuse keeps the R2 reuse loop working only on parts that are already proven.

**Website analogy, corrected:** an "About Us" page is a **feature** (what people click). The **foundation** is what every page shares: navigation, database, login, style. Skills are the reusable components (header, forms). Build the skeleton, make one page work, then pull out the components it shares.

## 7. Build order (agrees with the pasted plan, plus gates)
1. **Rehearsal-01** → event definitions v1 (moment-marking proven by hand)
2. **Feature 01**, closeout only, all HUMAN/DERIVE first; INFER skills added one at a time behind claim-audit
3. **Feature 05 One Cue** (reuses everything, needs no video)
4. **Feature 02 Read Before React** (adds socratic-dialogue + the coach-written read library)
5. **03 and 04** only after the archetype library and multi-player identification exist
