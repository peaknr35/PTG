---
type: resource and tools guide (what to read, use and look at)
project: PTG (Practice-to-Game Lab)
status: draft v0.1
updated: 2026-11-10
companions: 03_design/01_blueprint.md · 01_foundation/foundation.md · 02_research/library.md
rule: free first, one tool per job, and every tool's output gets hand-checked before it's trusted (P8)
tags: ✅ = confirmed in a search this session · ◻ = well-known, check the official site for current version/license
---

# PTG Resources: Articles, Tools and Things to Look At

Organized by **what you're trying to do**, in roadmap order. Each entry says *why it matters for PTG* and *what to watch out for*.

---

## 1. Learn to think in systems (Phase 0)

| Resource | Type | Why for PTG |
|---|---|---|
| ◻ Donella Meadows, *Thinking in Systems: A Primer* | Book | Stocks, flows, loops, delays, traps: the language of every audit we've done |
| ◻ Meadows, "Leverage Points: Places to Intervene in a System" (donellameadows.org) | Free essay | Which fixes matter (goals and information beat parameter tweaks) |
| ◻ Loopy (ncase.me/loopy) | Free tool | Draw and *animate* causal loops in a browser. Great for the KNOW/SEE/DO loop |
| ◻ Kumu (kumu.io) | Free tier | Maps of loops and stakeholders (player, parent, coach) |
| ◻ Insight Maker (insightmaker.com) / Vensim PLE (vensim.com) | Free | Simulate stocks and flows once you're ready for numbers |
| In your folder: *Introducing Feedback Thinking and System Dynamics Modeling* | PDF | A teaching approach for system dynamics |

**Practice habit:** draw one causal loop diagram a week for something real (your workout, a game, PTG).

---

## 2. Learn the sport science behind PTG (Phase 0)

### Books (the foundations the papers build on)
| Resource | Why for PTG | Principle |
|---|---|---|
| ◻ Button, Seifert, Chow, Araújo & Davids, *Dynamics of Skill Acquisition: An Ecological Dynamics Approach* (2nd ed.) | Constraints, affordances, representative design | P2 |
| ◻ Renshaw, Davids, Newcombe & Roberts, *The Constraints-Led Approach* | Turning theory into drills | P2 |
| ◻ Gabriele Wulf, *Attention and Motor Skill Learning* | External vs. internal focus, read critically | P7 |
| ◻ Joan Vickers, *Perception, Cognition, and Decision Training: The Quiet Eye in Action* | Gaze, quiet eye, decision training | P3, P15 |
| ◻ Schmidt & Lee, *Motor Control and Learning* | Retention vs. transfer, practice scheduling (the textbook) | P4, P10 |
| ◻ Farrow, Baker & MacMahon (eds.), *Developing Sport Expertise* | How expertise is built; perceptual-cognitive skill | P3, P4 |

### Papers (read in this order; all graded in 02_research/library.md)
1. Temporal occlusion training meta-analysis (Müller 2024): your best-supported principle (P3)
2. `s40279-023-01884-3.pdf` (⚠ MISSING from folder: re-download) (in folder): representative design for VR/AR (P2, P5)
3. Kittel et al. 2020 + softball 2024: broadcast vs. player's-eye view (P5)
4. Feedforward self-modeling + PETTLEP update: "visualize yourself" (P6)
5. Turner, "But did my athlete improve!? N = 1": noise bands (P9)
6. Sci Reports 2023 + Guo & Wang 2025: where experts look, and time pressure
7. ViSTAR and BasketballBench (in folder): what AI can and can't do in basketball (P12)
8. ExpertAF (CVPR 2025): words plus a shown correction (output standard)

---

## 3. Phase 1 tools: the hand-made Ground Truth Ledger (no code)

The goal: measure catch time, release time, defender distance and a **speed range** on your own clips, by hand.

| Job | Tool | Cost | Notes |
|---|---|---|---|
| Record | Your phone in **slow-mo** (120–240 fps) | Free | Higher fps = finer timing. Note the fps in the ledger |
| Step frame by frame, time, mark distances | ✅ **Kinovea** (kinovea.org): latest 2025.x | Free, open source | Built for sports video: slow, compare side by side, annotate, measure. Windows (Mac/Linux unconfirmed) |
| Alternative frame tracking | ◻ Tracker, Open Source Physics (physlets.org/tracker) | Free | Plots position over time; good for estimating defender speed |
| Quick frame stepping | ◻ VLC (frame-step key) | Free | Fine for counting frames |
| Time-aligned notes on video | ◻ ELAN (archive.mpi.nl/tla/elan) | Free | Tag "catch," "defender turns," "release" on a timeline |
| Ledger | Google Sheets / Excel | Free | One row per rep: fps, catch frame, release frame, distance marks, speed low/high |
| Floor reference | Tape marks every 3 ft + a tape measure | ~$10 | Gives the video a real scale, so distance isn't a guess |

**How to get a speed *range* (P15):** time the defender between two floor marks (frames ÷ fps). Repeat 3 times. Use the lowest and highest as your range. If the margin's sign flips inside that range, the answer is "can't tell."

---

## 4. Phase 1–2 tools: prototyping a film lesson without code

| Job | Tool | Cost | Why |
|---|---|---|---|
| Pause-and-predict video lesson | ◻ **H5P Interactive Video** (h5p.org) | Free (self-host) | Video stops at a time you set and asks a question. A no-code Film to Floor mock-up |
| Same, hosted | ◻ Edpuzzle / PlayPosit | Free tiers | Teacher tools for pause questions; quick to test with 5 players |
| Crop to player's-eye view (P5 test) | Kinovea zoom/crop, or any video editor | Free | Make a broadcast vs. cropped version of the same lesson |
| Annotate reads (pause point, cue, alternatives) | ◻ **ELAN**, or ✅ **CVAT** / **Label Studio** | Free, open source | CVAT is stronger for video and tracking; Label Studio is more general |
| Slides version | Google Slides / paper | Free | Fastest "paper prototype" |

---

## 5. Data sources (with rights warnings)

| Source | What | Rights |
|---|---|---|
| **Your own pickup film** | The best source: personal, legal, transferable | Blur or crop other players; get consent where you can |
| ✅ NBA SportVU 2015–16 tracking (Hugging Face `dcayton/nba_tracking_data_15_16`; GitHub `linouk23/NBA-Player-Movements`) | Player and ball (x, y) positions, 600+ games | Research and learning use; **check terms before any commercial use**. Great for studying real closeout distances and speeds |
| ✅ TrackID3x3 (github.com/open-starlab/TrackID3x3) | 3x3 basketball tracking video (indoor, outdoor, drone) | Check license; repo status unconfirmed |
| ◻ Ego-Exo4D (ego-exo4d-data.org) | Includes basketball with **first-person + third-person** video and expert commentary (ExpertAF trained on it) | Requires a license agreement; research use |
| ◻ BasketballBench (paper in folder) | 7,980 Q&A pairs over NBA clips | Check whether the data is released and under what terms |
| ✅ BASKET (CVPR 2025, UNC), github.com/yulupan00/BASKET, https://arxiv.org/abs/2503.20781 | ~4,477 hrs of highlight video, 32,232 players, rated on **20 fine-grained skills** | Check license. **Key finding: top video models do much worse than humans** at rating skill, which is a warning for AI form scoring, not a green light |
| ✅ BasketEvent (2026), https://arxiv.org/pdf/2607.21267 | Who did what and when: 51k player-level events with player and ball trajectories, 226 games | **Broadcast** footage, so domain shift to phone pickup film. BasketballBench reuses its test split |
| ✅ PL-NBA (2026), https://arxiv.org/pdf/2608.19646 | Possession-level NBA video dataset, multiple tasks | Size, labels and license UNKNOWN. Broadcast |
| ✅ "Prompt, Probe, Train, or Annotate?" amateur single-camera sports video (Sep 2026), https://www.emergentmind.com/papers/2609.28049 | 66 amateur (volleyball) matches: **no single approach wins at every stage**; static single-frame CV loses on motion and identity; **manual annotation stays competitive** | The best evidence for PTG's "hand-annotate first" plan on amateur film |
| NBA broadcast clips | — | **Copyrighted. Don't build on them without rights** (P14) |

**The SportVU data is worth looking at early.** It can turn the guessed numbers ("defender speed ~16 ft/s") into a real distribution from pro games. That's still pros, not pickup, but it's far better than invented constants.

---

## 6. Measurement and computer vision (later phases, research only for now)

| Tool | What | License / caution |
|---|---|---|
| ◻ MediaPipe Pose (github.com/google-ai-edge/mediapipe) | Phone-friendly 2D/3D body landmarks | Apache 2.0. Validity for jump-shot angles is **unproven** (shelf 2b) |
| ✅ OpenCap (opencap.ai, Stanford) | 3D movement from **two** phones; free, cloud-based | Research tool; still being compared with lab systems |
| ◻ MMPose (github.com/open-mmlab/mmpose) | Pose-estimation toolbox | Apache 2.0 |
| ◻ Ultralytics YOLO (ultralytics.com) | Player/ball detection | **AGPL-3.0: commercial use needs a paid license.** Matters for patents and products |
| ◻ SAM 2 (github.com/facebookresearch/sam2) | Segmenting and tracking objects in video | Apache 2.0 |
| ◻ ByteTrack (github.com/ifzhang/ByteTrack) | Multi-player tracking | MIT |
| ◻ FFmpeg (ffmpeg.org) | Cutting clips, extracting frames | LGPL/GPL |

**The rule for all of them:** run the tool and Kinovea hand timing on the same 20 reps. If they disagree by more than the difference you're trying to detect, the tool doesn't get used for that feature (P8).

---

## 7. Gaze ("where to look"), optional and research-grade

| Tool | What | Caution |
|---|---|---|
| ◻ Pupil Labs (pupil-labs.com) | Wearable eye tracking | Expensive; research-grade |
| ◻ WebGazer.js (webgazer.cs.brown.edu) | Webcam eye tracking in a browser | **Low accuracy.** Only good for rough "which half of the screen" during film lessons, never for quiet-eye timing |

For the MVP, "where to look" stays a **coach-annotated** field (grade C), not a measurement.

---

## 8. The AI layer (learn now, build much later)

| Resource | Why for PTG |
|---|---|
| ◻ Andrej Karpathy, "Intro to Large Language Models" (YouTube, ~1 hr) | How an LLM actually works, in plain terms |
| ◻ 3Blue1Brown, neural networks / transformers series (YouTube) | Visual intuition for what's inside |
| ◻ Anthropic API docs: prompt engineering, tool use, structured outputs, prompt caching (docs.anthropic.com / platform.claude.com) | Structured outputs = how the mentor card stays in its form (P12) |
| In your folder: *The System Prompt Illusion* | What a persona prompt does and doesn't change |
| In your folder: *Thinking Twice* | Designing for reflective judgment (your judgment log) |
| In your folder: *Contract-Based Agentic Systems Engineering* | Contracts between stages and agents |
| ◻ Pydantic (Python) / Zod (JavaScript) | Strict schemas for data and AI outputs, so a card missing a field gets rejected by code, not by hope |
| ◻ promptfoo (github.com/promptfoo/promptfoo) | Open-source **evals**: automatically test outputs against rules, like the claim audit |
| ✅ ICM paper + your ICM playbook | How the dev workspace is organized |
| ◻ PRIMM (teachcomputing.org) | Staying in control of AI-written code |

---

## 9. Security and privacy

| Resource | Why |
|---|---|
| ✅ OWASP Top 10 for LLM Applications 2025 (genai.owasp.org) | Prompt injection, data leaks, excessive agency. Read the official text, not blogs |
| ◻ OWASP ASVS (Application Security Verification Standard) | A checklist for the non-AI parts (login, storage) |
| ◻ NIST AI Risk Management Framework (nist.gov) | A plain structure for AI risk: map, measure, manage, govern |
| ◻ NIST Privacy Framework | Same idea for privacy |
| ✅ FTC amended COPPA rule (Federal Register, Apr 22 2025; compliance Apr 22 2026) | If you ever serve kids under 13 |
| ◻ Illinois BIPA (ilga.gov) | The strictest US biometric law. Ask a lawyer whether pose data counts |
| ◻ deface (github.com/ORB-HD/deface) | Open-source automatic face blurring for video, for bystanders |

---

## 10. Patents, trademarks, IP

| Resource | Why |
|---|---|
| ◻ Google Patents (patents.google.com) | Free prior-art search. Try "basketball decision training video occlusion," "closeout distance tracking," "coaching feedback retention test" |
| ◻ USPTO Patent Public Search (ppubs.uspto.gov) | The official search |
| ◻ USPTO Trademark Search (tmsearch.uspto.gov) | Check "Practice-to-Game Lab" before using it publicly |
| ◻ USPTO Patent Pro Bono Program + Inventors Assistance Center (uspto.gov) | Free or low-cost help if you qualify |
| ◻ Law school IP clinics | Often free for individual inventors |
| ✅ USPTO AI eligibility guidance 2025–26 (see blueprint §13) | Why "technical improvement" wording matters |
| **Invention log** | A dated notebook (or these files): ideas, diagrams, decisions |

---

## 11. Market and user research

| Resource | Why |
|---|---|
| ◻ Rob Fitzpatrick, *The Mom Test* | How to interview hoopers without them just being nice to you |
| App Store reviews of HooperIQ, HooperLab, HoopStack, HomeCourt, Monona (1–3 stars) | Unmet needs, in users' own words |
| ◻ Reddit: r/BasketballTips, r/basketballcoach | What players and coaches actually complain about |
| ◻ Product Hunt | New launches in sports AI |
| 5 interviews with solo hoopers | "What practice feels fake?" "Has film ever changed your game?" |

---

## 12. Staying current (a loop, not a pile)

| Tool | Use |
|---|---|
| ◻ Google Scholar alerts | Alerts for "temporal occlusion basketball," "representative learning design," "self-modeling basketball," "markerless pose validity shooting" |
| ◻ Semantic Scholar / Connected Papers (connectedpapers.com) | See what cites a key paper, and what's newer |
| ◻ alphaXiv (alphaxiv.org) | Discuss and annotate arXiv papers |
| ◻ Zotero (zotero.org) | Free reference manager: one library, tagged by shelf |

**The update loop:**
```
Alert → read abstract → worth it? → full read → evidence card (graded, with "what this does NOT justify")
     → does it change a principle? → reviewed revision of 01_foundation/foundation.md (new version + date)
```
Once a month, about an hour. If the library grows but no principle ever changes, you're collecting, not learning.

---

## 13. The short list: start with these 10

1. *Thinking in Systems* (ch. 1–2)
2. Loopy: draw the KNOW/SEE/DO loop
3. Temporal occlusion meta-analysis (Müller 2024)
4. Kinovea + slow-mo + floor tape: build your first Ground Truth Ledger
5. NBA SportVU 2015–16: look up real closeout speeds
6. H5P Interactive Video: mock one pause-and-predict lesson on your own clip
7. *The Mom Test*, then 5 interviews
8. OWASP LLM Top 10 (official)
9. Google Patents: search the 4 candidate ideas
10. Zotero + Scholar alerts: set up the update loop

---

## Sources confirmed this session
- Kinovea: https://kinovea.org/ · 2025.1 release notes: https://kinovea.org/download/releases/2025.1.html
- NBA tracking 2015–16 (Hugging Face): https://huggingface.co/datasets/dcayton/nba_tracking_data_15_16 · loader: https://huggingface.co/datasets/dcayton/nba_tracking_data_15_16/blob/main/nba_tracking_data_15_16.py
- NBAsportvu R package: https://rdrr.io/github/imadmali/NBAsportvu
- TrackID3x3 paper: https://arxiv.org/html/2503.18282v2 · DAGnet datasets: https://github.com/alexmonti19/dagnet/blob/master/datasets/README.md · SMOGS (college SportVU): https://arxiv.org/pdf/1806.06696
- OpenCap (Stanford, 2025 study): https://news.stanford.edu/stories/2025/11/smartphones-monitoring-neuromuscular-disorder-treatment-research · https://mobilize.stanford.edu/?p=6174
- CVAT vs. Label Studio: https://roboflow.com/compare-labeling-tools/cvat-vs-label-studio · https://www.cvat.ai/resources/blog/best-open-source-data-annotation-tools
