---
type: session audit (retrospective of the whole PTG ideation session)
session: 2026-11-10 → 2026-11-11 · 50 founder messages · Claude Code + other AI chats (alphaXiv "Aurelle" and others)
source: _archive/chat-transcripts/claude-session-01_conversation.md (full markdown) · claude-session-01_full-transcript.zip (raw export)
labels: OBSERVED = visible in the transcript or files · INFERRED = reasoned from them · HYPOTHESIS = needs testing
---

# Session Audit: How PTG Went From Passion to Foundation

## 1. Bottom line
- **What got built:** a researched, structured *foundation*: 38 markdown files, 35 sorted papers, a graded evidence library, 15 principles, a glossary, a PRD (v1.1, 8 BRs and 17 tested FRs), a gated roadmap, a CV blueprint, a skills architecture, a rehearsal kit and a published concept site. (OBSERVED)
- **What did not happen:** zero clips recorded, zero marks, zero ledger rows, zero tutor answers, zero buyer conversations. `predictions.md` is still blank. (OBSERVED)
- **The core finding:** the session ran the exact loop PTG exists to fix. **KNOW grew every hour; SEE and DO never got a rep.** Documents are film study. Rehearsal-01 is the first floor rep. (INFERRED, and the strongest finding in this audit)

## 2. Timeline (what the founder did, in phases)
| Phase | Founder messages | What happened | Output |
|---|---|---|---|
| 1. Spark | 1 | Shared the lived problem (90% of practice alone, NBA dream) + a competitor's post (HoopStack); asked for standout ideas | 5 ideas, competitor framing |
| 2. Set the lens | 2–4 | Pasted a Meadows audit prompt, then a v2 prompt; asked for "latest articles"; set "think like a system thinker" | Systems audits, memory saved |
| 3. Bring outside AI work | 5–14 | Pasted 4 PDFs and a stream of other-AI answers (alphaXiv "Aurelle" and others): Auditor, simulator, index.html, index2.html, ICM design | Audits of each; research library; v1 → v2 board critique |
| 4. Plan | 15–21 | Asked for MVP/PRD, little-by-little plan; NBA-mentor vision; Curry visualization story | MVP PRD, mentor reframe, Film to Floor, foundation |
| 5. Deep dive | 22–30 | "Don't build, learn everything": competitors, security, patents, resources, CV blueprint, YOLO matrices, VFR, labeling QC | Blueprint, resources, CV blueprint, CV methods |
| 6. Method layer | 31–35 | DDIA book + APIV talk; life goal; frameworks papers; skills/feature agents | System design, glossary, goal map, frameworks, skills architecture |
| 7. Thinking reset | 36–37 | Asked for tutor mode ("think, don't build") | Tutor mode set; Question 1 asked |
| 8. Structure + ship | 38–50 | Reverted to building: ICM restructure, recap archive, roadmap/to-do/PRD, concept site, merged simulators, concept widened, reviewer rule, this audit | Organized workspace, site v5, PRD v1.1 |

## 3. What the founder did well (keep doing it)
| Habit | Why it works (systems view) |
|---|---|
| **Started from a lived problem, not a technology** | The problem framing ("practice doesn't carry to games" → "don't know what to fix") stayed stable through every run |
| **Ran several AIs and brought their answers back for review** | This is the ensemble method from the SDS 2026 CLD paper, done by hand: the same diagnosis appearing in 5+ independent runs is the closest thing to confidence the session has |
| **Saved the actual PDFs into the folder** | Let claims be checked against text (READ), not just search summaries (SNIPPET). Caught misapplied papers |
| **Pushed for "latest research" every time** | Forced a grading system to exist; without the pushback, invented science would have entered the rules |
| **Stated a life goal** | Exposed a real mismatch (businesses vs. players) and produced a clean decision: learning lab + portfolio |
| **Made explicit decisions when asked** | Learning lab, reviewer rule, concept widening, restructure approval: each is logged with a date |
| **Asked for structure before building** | ICM restructure made the work walkable at ~2.5k tokens per task |

## 4. How the research was found (the input pipeline)
| Source | How it entered | Quality | Catch rate |
|---|---|---|---|
| Founder-downloaded PDFs (35 kept, 8 duplicates) | Dragged into the folder, often after another AI cited them | High when READ | Several misapplied by other AIs (BASKET, the rebound paper) |
| Other AI chats (alphaXiv) | Pasted answers with citations | **Mixed**: real papers, but often with invented numbers or claims the paper doesn't make | ~1 serious error per paste |
| Claude web searches (~35) | `standard` mode, 2–6 queries in parallel, then fetch for specific tables | Search summaries = SNIPPET; official docs = ✅ | Found HooperIQ (killed "freeze-frame is unique"), YOLO tables, COPPA dates |
| Claude PDF reads (pypdf text extraction) | Abstracts, limitations sections, keyword hits | READ | Closed UNKNOWNs (ViSTAR's own no-defender limitation) |
| Session export | `export_transcript` → markdown | Verbatim | Source for the case study |

**Pattern:** the best evidence came from **papers the founder saved + their limitations sections**. The weakest came from **confident summaries in pasted answers**.

## 5. The AI-audit catch log (portfolio raw material)
Every row was caught by checking a claim against a source or the project's own rules. (OBSERVED in the transcript)

| # | What another AI wrote | What was actually true | Rule it broke |
|---|---|---|---|
| 1 | 72% solo shooting, contest/rush penalties, "Transfer Likelihood" (index.html) | Invented constants; "transfer" was just a ratio of the made-up numbers | P8, wrong goal |
| 2 | Bottleneck chosen by if/else order | The code's order, not the data, picked the weakness | Hidden rule (#5) |
| 3 | Canvas colors via `var(--x)` | Canvas ignores CSS variables | Bug |
| 4 | Defender stops at boundary in one formula, runs through the shooter in another (index2) | Two stopping rules for one defender | Hidden assumption |
| 5 | "Representativeness 11%" and "1 out of 10" for the same workout | Scoring undefined | Precision illusion |
| 6 | Affordance thresholds (>14 ft shoot, 8–12 ft attack, <6 ft counter) with gaps at 6–8 and 12–14 ft | Invented, and incomplete | P8 |
| 7 | "Strictly prohibit internal cues" | Evidence favors external focus but no basketball retention meta-analysis exists | P7 blanket ban |
| 8 | "See It Before You Grab It" supports training players to anticipate (twice) | It's a computer predicting rebounds | Misapplied paper |
| 9 | 2026 Sci Reports gaze specifics (defender hips, ball-handler chest) | Paper not findable in search; specifics unsupported | Unverified citation |
| 10 | "Curry tracks defender hip rotation" | Mind-reading a real player | P14 |
| 11 | BASKET "validates motion economy" | BASKET shows AI skill rating is far below humans | Misread finding |
| 12 | "Elite release 0.55–0.68 s", "closeout 14–18.5 ft/s", ±0.15/−0.05 states | No sources; the states were index.html v1's thresholds reused | P8 |
| 13 | "Kinovea steps to 0.01 s" | One frame = 0.0167 s at 60 fps | Factual error |
| 14 | "Lower your hips" cue in a doc that banned internal cues | Self-contradiction | Consistency |
| 15 | "Normalized skeleton ratios strip biometric identity" | Not established; ratios are body proportions | Privacy overclaim |
| 16 | "SOC-2 & COPPA-compliant vault", "cryptographic consent" | Compliance isn't a storage property | Buzzwords |
| 17 | Patent the timing math itself | High prior-art risk; likely abstract | Weakest IP target |
| 18 | Roadmap gates: "players verify the drill helped", "20 runs of external focus" | Satisfaction and obedience, not learning | Wrong gates |
| 19 | Code: missing `Tuple` import | The module crashes on load | Bug |
| 20 | Code: angles from normalized x/y | Every angle wrong on 16:9 video | Bug |
| 21 | Code: `if frame.dts` treats 0 as missing; silent timestamp reuse | Guess instead of flag | Bug |
| 22 | Catch = "both hands"; contest = "shooter's cylinder"; margin per frame | Definitions drifted from v0; wrong granularity | Ontology drift |
| 23 | YOLO26m AP50 = 89.9 | Official page: 89.6 | Number error |
| 24 | Congratulated the founder on HoopStack's App Store update | HoopStack was a competitor's post | Context misread |
| 25 | Challenge-point "60–75% sweet spot, >85% / <40%" | Real framework, invented thresholds | P8 |

**Pattern across 25 catches:** each new AI version was **more polished and more confident**, while the unverified core stayed the same. Polish rose; accuracy didn't.

## 6. Claude's own mistakes in this session (same standard applied)
| Mistake | Effect | Fix |
|---|---|---|
| Produced ~20 large documents while repeatedly saying "stop designing, go measure" | Contributed to the KNOW-without-DO loop | Named it in every summary; tutor mode added. Still a real failure of restraint |
| Deleted PRD v0.2 from the blueprint **before** writing PRD v1.0 | Brief window where the PRD existed only in context | v1.0 written immediately; lesson: write the new home first, then point |
| Wrote "his career" about the founder on the site | Pronoun assumption | Fixed to neutral wording before sharing |
| An Edit failed silently-in-batch (BR-8 row) | PRD missing BR-8 until rechecked | Grepped and fixed |
| Many library entries are SNIPPET (search summaries), not READ | Grades above B not justified yet | Labeled honestly; reading list prioritizes the key papers |
| Couldn't run the site's script in the preview | Interactive lab verified by syntax + math only | Stated in the reply; needs one live click-through |
| Called `s40279-…pdf` "in folder" early; it later went missing | Three docs cite a missing file | Flagged everywhere as MISSING |
| Tutor mode started, then the next requests were build requests and were done | Question 1 still unanswered | The founder's call to build was honored, but the thinking rep stayed open |

## 7. The session as a system
```
R1 (ran hot):  paste AI output → audit → better doc → more confidence → paste next AI output
R2 (ran):      research → grading rules → stricter audits → better research
B1 (MISSING):  real clip → marks → data contradicts a plan → plan changes
Stock that grew:   documents (38), evidence cards, principles
Stock that didn't: measured reps, predictions, conversations
Trap active:       KNOW without DO (the founder's film-study trap, now as founder)
Leverage now:      #6 information: one real data point beats the next document
```
**Prediction (HYPOTHESIS):** the first rehearsal will change at least one "frozen-looking" definition (contest proxy or release) and at least one design assumption (CFR vs. VFR). If it doesn't, the design was better than expected. Log the result.

## 8. Scorecard (0–10, honest)
| Dimension | Score | Why |
|---|---|---|
| Problem clarity | 8 | Lived, stable across runs, widened cleanly |
| Evidence discipline | 8 | Grades, catches, READ vs. SNIPPET; key papers still unread in full |
| Structure / walkability | 9 | ICM restructure, routers, one home per fact |
| Decision hygiene | 8 | Every decision dated in a log |
| Contact with reality | **1** | No clips, no marks, no players, no buyers |
| Founder's own reasoning reps | 2 | Predictions asked for many times, none written yet |
| Business progress | 1 | Buyer group not named |

## 9. What to do next (in this order)
1. **Answer tutor question 1** (a real game that went wrong and what you practiced after). 10 minutes.
2. **Fill `04_experiments/rehearsal-01/predictions.md`**, then record clips 01–03.
3. **Write the case study** "How I audit AI-built systems" from §5 of this audit.
4. **Name one buyer group** and book one conversation.
5. **Freeze document production** until steps 1–2 are done. New docs only if real data demands them.
