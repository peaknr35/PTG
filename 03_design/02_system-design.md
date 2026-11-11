---
type: system design layer (how data and work flow), design only, no code
project: PTG (Practice-to-Game Lab)
status: draft v0.1
updated: 2026-11-10
sources: Kleppmann, *Designing Data-Intensive Applications* (DDIA, PDF in folder; concepts summarized in my own words, chapter numbers from its table of contents) · "Why Your AI Code Turns Into Slop" talk (Kem Tossoun, APIV loop + 14-step AI delivery life cycle; pasted transcript)
companions: 01_foundation/foundation.md (why) · 01_foundation/glossary.md (shared language) · 03_design/05_cv-methods.md · 03_design/01_blueprint.md
---

# PTG System Design: DDIA + the AI Delivery Life Cycle

## 0. Three layers, three questions

| Layer | Question | Source | PTG file |
|---|---|---|---|
| **Systems thinking** | *Why* does the system behave this way over time? | Meadows | 01_foundation/foundation.md, audits |
| **System design** | *How* does data flow, get stored, stay correct and change? | DDIA | **this file** |
| **Delivery discipline** | *How* do I get an AI to build it without slop? | APIV + 14 steps | **this file**, §3 |

You asked to become a systems thinker, not a builder. These three layers are how a systems thinker directs builders, human or AI.

---

## 1. The big idea from DDIA: one immutable log, everything else derived

DDIA's last part (ch. 11–12) separates **systems of record** (the original facts, written once) from **derived data** (anything you can recompute: views, summaries, caches, indexes). PTG has been inching toward this already ("raw → reviewed → derived → generated, never overwrite raw"). Make it the spine:

```
                    ┌──────────── SYSTEM OF RECORD (append-only event log) ────────────┐
                    │ ClipRecorded · TimestampsExtracted · EventMarked(pass, annotator) │
                    │ MarkAdjudicated · PredictionLogged · LessonProposed               │
                    │ RetestResultLogged · LessonPromoted / LessonDropped               │
                    │ GameTagLogged · DefinitionVersionPublished · ModelRunCompleted    │
                    └──────────────────────────────┬─────────────────────────────────────┘
                                                   │ (rebuild any time)
        ┌──────────────┬───────────────┬──────────┴─────┬────────────────┬──────────────┐
     Ledger view   Agreement stats   Lesson memory    Mentor cards    Judgment log   Dashboards
     (derived)     (derived)         (derived)        (generated)     (derived)      (derived)
```

**Why this fits PTG so well:**
- **Nothing vanishes.** A wrong mark isn't edited; a correction is a new event. Audit trails come for free (P1).
- **Re-run with new rules.** New definition version or new model → replay the log → new derived views. The old ones stay comparable.
- **Lesson memory can't silently drift.** "Proven for you" is just a view over `LessonPromoted` events, each pointing to its retest evidence (P10).
- **It's the clearest "technical" shape** for the patent conversation: evidence-gated promotion as an event-driven state machine (ask your attorney; novelty UNKNOWN).

---

## 2. DDIA chapter by chapter, mapped to PTG

| DDIA chapter | Core idea (my words) | Where it shows up in PTG | Design rule |
|---|---|---|---|
| **1 Reliable, scalable, maintainable** | Faults will happen (hardware, software, **human**); describe load and performance with real numbers; keep systems operable, simple, evolvable | Human faults = annotator drift and mis-marks. Load = **labeling hours per week**, not requests per second | Design for human error first: double marks, hidden gold, kill switches |
| **2 Data models** | Pick the model that fits the relationships: document (self-contained), relational (many-to-many), graph (rich links) | Player sessions ≈ documents; labels ↔ clips ↔ definitions ≈ relational; **foundation ↔ evidence ↔ features ≈ graph** | Keep the evidence library as a graph: principle → evidence card → feature → test |
| **3 Storage and retrieval** | Append-only logs, indexes built from them | The event log above; indexes = ledger views | Write once, index many |
| **4 Encoding and evolution** | Schemas change; old and new code must read each other's data (backward/forward compatibility) | Event definitions v0 → v1, label schema, card format | Every record carries `schema_version` and `definition_version`; never reuse a field name with a new meaning |
| **5 Replication** | Copies of data; one leader accepts writes | Phone + laptop + cloud copies of clips | **One writer per fact** (e.g., only the adjudicator writes `MarkAdjudicated`) |
| **6 Partitioning** | Split data so it scales | Per player, per session | Partition by player (privacy and deletion get easy) |
| **7 Transactions** | Concurrent changes cause anomalies; isolation prevents them | Two passes or two annotators touching the same item; promoting a lesson while a retest is still being logged | Promotion only reads **completed** retest events; blind passes can't see each other's marks |
| **8 Trouble with distributed systems** | **Clocks are unreliable**: time-of-day clocks jump, monotonic clocks only measure intervals, clocks drift; networks and processes fail partway | **Exactly the phone timestamp problem**: VFR, multi-camera drift, file timestamps vs. capture time | Use per-frame timestamps for intervals; model drift (`t_global = a·t + b`); never trust a wall-clock to order events across devices |
| **9 Consistency and consensus** | Getting several nodes to agree on one value is hard and has rules | Annotators disagreeing → adjudication | One adjudicator decides; log the decision with its reason |
| **10 Batch processing** | Immutable inputs → deterministic jobs → derived outputs you can always rebuild | Model runs over a session's clips | Same input + same version = same output (NFR5); outputs tagged with version |
| **11 Stream processing** | Events flow continuously; derived views update; event sourcing | Game tags and logs arriving over weeks | Views update from events; a late retest event updates lesson status |
| **12 The future of data systems** | Systems of record vs. derived data; **end-to-end correctness** (checks at the edges, not just inside); **audit, "trust but verify"**; ethics, privacy and surveillance | The claim audit, Workflow C re-marks, consent and deletion | Audit continuously; minimize what you collect; deletion must reach derived data too |

### The DDIA questions to ask in every PTG design review
1. What's the **system of record**, and what's derived?
2. **Who is the one writer** of this fact?
3. What happens when this **schema/definition changes**? Can old data still be read?
4. Which **clock** does this timestamp come from, and can it drift or jump?
5. If this step **fails halfway**, what state is left behind?
6. How do we **verify end to end** that the final card matches the raw evidence?
7. If a player asks to be **deleted**, does every derived copy go too?

---

## 3. The AI delivery life cycle (from the talk), mapped to PTG

### 3.1 APIV: Align → Plan → Implement → Verify (then loop)
The talk's main claim: **most of the work is alignment**, and the presenter says ~99% of it. Treat that number as his opinion, not a measurement. If alignment is done properly, plan, implement and verify become close to automatic. A loose request gives the model room to fill gaps with "median" code.

**For PTG this is the bridge to "system thinker, not builder":** you own Align (and sign-off on Verify). The agent does Plan → Implement inside your contracts.

### 3.2 The 14 steps, PTG status
Acts 1–2 (steps 1–7) are alignment, and **nothing gets built before step 8**.

| # | Step (from the talk) | What it leaves behind | PTG status |
|---|---|---|---|
| 1 | **Outcome first**: north star, business requirements, the felt experience, the acceptance bar; no architecture | Brief + **numbered** business requirements | ⚠ Partly: PRD v0.2 has jobs and features, but requirements aren't numbered as BR-1, BR-2… |
| 2 | **One shared language** (ontology) | A dictionary of terms used the same way everywhere | ❌ → **now 01_foundation/glossary.md** (we already drifted: "defender arrival" → "contest proxy") |
| 3 | **Pattern pass**: research how the problem was solved before; reuse open source | Census of existing solutions | ✅ Mostly: Kinovea, CVAT, COCO, ffprobe, event sourcing (DDIA), ICM |
| 4 | **Design on boards**: iterate visuals in a throwaway space, then build clean from the signed-off board | Signed-off design boards | ✅ **03_design/boards/v1-invented-formulas.html and 03_design/boards/v2-honest-model.html are boards.** Never promote board code to the product; rebuild from the signed-off design |
| 5 | **Enumerate before you build**: every case and combination | Case matrix | ❌ Next: conditions × observable × CFR/VFR × handedness × outcome (§4) |
| 6 | **Model the data**: source of truth, rebuildable derived store, one writer per fact | Data model + module map | ⚠ Started (CV sensor contracts); the event log in §1 completes it |
| 7 | **Requirement ledger**: business req → functional req → design → code → test | Traceability ledger | ❌ Needed before step 8 |
| 8 | **Plan as a contract** | Phased plan with gates | ✅ Roadmaps exist (blueprint §9, CV §10). Convert to a contract once 1–7 are done |
| 9 | Implementation discipline | Lane contracts and reports | Later (agent) |
| 10 | Verification layer | Proofs and budgets | Gates G0–G5 exist |
| 11 | Live integrity ("doctor") | Health checks | Workflow C + kill switches |
| 12 | Closing ritual | Updated docs and plan | Decision log |
| 13 | Lessons learned → back to alignment | Lessons | Judgment log + debrief |
| 14 | (Not clearly named in the transcript) | — | UNKNOWN |

### 3.3 The "golden rules" from the talk, in PTG terms
| Rule (as stated) | PTG version |
|---|---|
| Write it down or it didn't exist | Every decision, prediction and mark is an event (§1) |
| Nothing vanishes | Append-only; corrections are new events |
| Pay for each mistake once | Every failure becomes a rule in the foundation or a check in the gates |
| One rule lives in one place | Principles only in 01_foundation/foundation.md; terms only in 01_foundation/glossary.md; other files *link*, never restate |

### 3.4 Why iteration makes "slop" (his claim, as a hypothesis)
He says iterating on live code ("move it left… now change the color") makes AI pile on correction code instead of rewriting, which bloats files and slows them down. He mentions benchmarks but doesn't show them, so **treat it as UNVERIFIED**. The fix is cheap regardless: **iterate on boards, then build once from the signed-off version.** That's how 03_design/boards/v1-invented-formulas.html → 03_design/boards/v2-honest-model.html should be treated.

---

## 4. Step 5 for PTG: the first case matrix (enumerate before building)

The rehearsal's world, enumerated. Every combination needs a defined behavior **before** any code.

| Dimension | Values |
|---|---|
| Condition | solo · simulated · live |
| Frame timing | CFR · VFR · not checked |
| Release | observable · NO (blur/occl/out/ambig) |
| Contest proxy | observable · NONE (never reached the ring) · NO · n/a (solo/simulated) |
| Handedness | right · left |
| Pass | P1 · P2 · adjudicated |
| Outcome | make · miss · unknown |

**Examples of required behavior:**
| Case | Required behavior |
|---|---|
| live + VFR + both events observable | margin from PTS; shown with range |
| live + not checked + both observable | margin from frames ÷ fps, flagged "assumes CFR" |
| live + contest = NONE | margin = "no contest before release" (not 0, not blank) |
| live + release = NO | no margin; rep counts toward the unobservable rate |
| simulated + cue logged | cue time stored separately; never shown as a contest |
| P1 and P2 disagree beyond tolerance | goes to adjudication; no derived value until resolved |
| outcome = unknown | excluded from make % and **counted** in % unknown |

---

## 5. Step 7 for PTG: the requirement ledger (format)

```
BR-1  Players can see whether a contest reached the ball before release on their own clips
  FR-1.1  Mark release and contest proxy per event-definitions v1        → test: 2-pass agreement (G1)
  FR-1.2  Compute margin from PTS                                          → test: CFR case = frames÷fps
  FR-1.3  Show margin as a range; label only if the sign holds (P15)       → test: sign-flip case → "can't tell"
  Design: Appendix A ledger, CV methods §4
  Status: designed · not built · rehearsal pending
```
Every feature in every PRD should end up as a BR → FR → test chain. A feature with no BR is scope creep. A BR with no test can't be signed off.

---

## 6. What to do with this (in order, no code)

1. **Run rehearsal-01** (already prepped). It's the first real data for the event log.
2. **Adopt the glossary** (01_foundation/glossary.md). From now on, use only these words in prompts to any AI.
3. **Number the business requirements** (BR-1…) from PRD v0.2.
4. **Fill the case matrix** (§4) for the 20-rep session after the rehearsal fixes the definitions.
5. **Draft the event list** (§1) as plain sentences: what event, who writes it, what it points to.
6. Only then, step 8: turn the roadmap into a plan-as-contract for a coding agent.

---

## 7. Systems check on this layer

- **Balancing loop added:** the requirement ledger catches scope drift (a feature with no BR gets cut).
- **Leverage:** shared language (#6 information) and one-writer rules (#5 rules) are higher leverage than any tool choice.
- **The risk:** a 14-step lifecycle for a solo founder can turn into bureaucracy, more documents and still no clips. **Scale it to the stage:** PTG is at Act 1. Do steps 1, 2, 5 and 7 lightly, and let the rehearsal data shape step 6.
