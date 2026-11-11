---
type: computer vision architecture blueprint + sensor PRD (design only, no code)
project: PTG (Practice-to-Game Lab)
status: draft v0.1
updated: 2026-11-10
governed by: 01_foundation/foundation.md (especially P8 "measure only what the sensor can measure", P9, P12, P15)
companions: 03_design/01_blueprint.md · 02_research/resources.md · 02_research/library.md
evidence tags: ✅ confirmed from official docs/pages this session · ◻ well-known, verify before relying · ⚠ claim from another chat, not checked by me
---

# PTG Computer Vision Blueprint: Pose Estimation and Biomechanical Tracking

## 0. The systems frame (read this first)

**In PTG, computer vision is a sensor, not a coach.** Its only job is to turn video into **timestamps and positions** that are as trustworthy as a careful human marking the same clip in Kinovea. Everything downstream (engine, mentor card, lessons) inherits its errors.

```
Video → [CV sensor] → events + positions (with error bars) → engine → card → player
                ▲                                                         │
                └──── validation loop: Kinovea hand marks ◄───────────────┘
                      (if the sensor drifts from the hand marks, the sensor is switched off for that feature)
```

**Three rules that come before any model choice:**
1. **Validity on *our* footage beats benchmark accuracy.** COCO mAP measures photos of people on Flickr, not a jump shot filmed on a phone at 240 fps. (P8)
2. **A feature ships only after it matches human hand marks within the human-to-human disagreement.** (P8, P9)
3. **Offline is fine.** PTG analyzes a session *after* practice. Real-time speed is a nice-to-have; validity isn't. That changes which numbers in the matrices actually matter.

**The trap this blueprint is built to avoid:** "seeking the wrong goal," meaning picking the model with the highest mAP and assuming the jump-shot numbers are right.

---

## 1. What PTG wants to measure (the measurement catalog)

Each feature gets: what it is → what it needs → can COCO-17 do it? → current status.

| # | Feature | Definition (fixed before recording) | Needs | COCO-17 alone? | Status |
|---|---|---|---|---|---|
| F1 | **Catch frame** | First frame of hand-ball contact | Ball + hands | ❌ no ball, no fingers. Wrist ≈ hand | Needs ball detector + wrist proximity → validate |
| F2 | **Release frame** | First frame with no finger-ball contact | Ball + fingers | ❌ same problem, plus **motion blur** at release | Hardest event; validate at 120–240 fps |
| F3 | **Execution time** | F2 − F1 (frames ÷ fps) | F1, F2 | Derived | Valid only if F1/F2 pass |
| F4 | **Dip timing** | Frame of lowest hip point after catch | Hips (11, 12) | ✅ hip keypoints exist | Good candidate (large, slow-ish motion) |
| F5 | **Jump height proxy** | Peak hip rise vs. standing, in calibrated units | Hips + line calibration | ✅ | Good candidate; needs camera calibration |
| F6 | **Landing displacement** | Ankle midpoint at landing vs. takeoff, on the floor plane | Ankles (15, 16) + floor-plane calibration | ⚠ ankles only, no heel/toe | Candidate; ankle accuracy is weaker in validation studies (shelf 2b) |
| F7 | **Knee/hip/elbow angles** | Sagittal-plane angles at set point and release | Shoulder/elbow/wrist/hip/knee/ankle | ⚠ 2D only, view-dependent | **Research only.** Reported markerless error of several degrees, larger out of plane (⚠ the other chat's review: ~3–15° sagittal) |
| F8 | **Rhythm consistency** | Spread of F3 and F4 timing across reps | F3, F4 | Derived | Good candidate (variability, not absolute) |
| F9 | **Contest proxy** (was "defender arrival") | First frame a visible contesting hand enters a **pre-defined image-space boundary** around the ball (boundary written down before annotating). "Not observable" if hand or ball is hidden. It's a **video proxy**, not the moment the shot became unavailable | Multi-person identity | ❌ identity is where automation fails on amateur film (✅ amateur single-camera paper) | **Manual only** in MVP |
| F10 | **Gaze / where to look** | — | Eye tracking | ❌ | Coach-annotated (grade C), not measured |

**What this table teaches:** the three features PTG cares about most (catch, release, defender arrival) are exactly the ones **COCO-17 pose can't see by itself.** Pose helps with the *body* (dip, jump, landing). The *events* need a ball detector and careful validation.

---

## 2. Dataset specification: COCO-Pose (17 keypoints)

### 2.1 Facts (✅ Ultralytics COCO-Pose docs)
| Item | Value |
|---|---|
| Train | 56,599 images (`train2017`) |
| Val | 2,346 images (`val2017`) |
| Test-dev | 20,288 images (labels withheld; scored on the COCO server) |
| Labeled total | 58,945 images, 156,165 annotated people |
| Keypoints | 17 per person, `kpt_shape: [17, 3]` = (x, y, visibility) |
| Classes | 1 (`person`) |
| Download | ~20.2 GB (images + labels) |
| License | COCO listed as **CC BY 4.0** (images also carry Flickr terms, so check COCO's terms of use); the Ultralytics YAML is AGPL-3.0 |
| Citation | Lin et al., "Microsoft COCO: Common Objects in Context," arXiv:1405.0312 |

### 2.2 The 17 keypoints and what PTG uses them for
| Idx | Name | Flip ↔ | PTG use |
|---|---|---|---|
| 0 | nose | 0 | Head direction proxy (not gaze) |
| 1 / 2 | left / right eye | 2 / 1 | Low value (tiny, often occluded) |
| 3 / 4 | left / right ear | 4 / 3 | Head orientation proxy |
| 5 / 6 | left / right shoulder | 6 / 5 | Shooting-side shoulder for elbow angle (F7) |
| 7 / 8 | left / right elbow | 8 / 7 | F7 (research only) |
| 9 / 10 | left / right wrist | 10 / 9 | **Hand proxy** for catch/release with the ball detector (F1, F2) |
| 11 / 12 | left / right hip | 12 / 11 | **Dip (F4), jump height (F5)**: the most useful points |
| 13 / 14 | left / right knee | 14 / 13 | Knee bend (F7) |
| 15 / 16 | left / right ankle | 16 / 15 | Landing displacement (F6) |

Flip index (✅): `[0, 2, 1, 4, 3, 6, 5, 8, 7, 10, 9, 12, 11, 14, 13, 16, 15]`. Used when images are mirrored during training. Left-handed shooters need the same care in feature code.

**Visibility flag** (COCO convention ◻): `0` = not labeled · `1` = labeled but occluded · `2` = visible. PTG rule: features built only on keypoints with **visibility/confidence above a threshold set during validation**, never on guessed occluded points.

**Accuracy metric** (◻): COCO keypoint mAP is based on **OKS (Object Keypoint Similarity)**, a per-keypoint tolerance that scales with person size. **It measures "close enough on a photo," not "correct to the frame on a fast jump shot."**

### 2.3 Why COCO isn't enough for basketball (gaps)
| Gap | Effect on PTG | Option |
|---|---|---|
| No **ball** | Can't detect catch/release from pose | Add a ball detector (COCO detection has a "sports ball" class ◻), validate |
| No **fingers/hands** | Wrist ≠ fingertips at release | Whole-body skeletons with hands and feet exist (COCO-WholeBody, 133 points ◻; MediaPipe 33 incl. heel/foot ◻) |
| No **heel/toe** | Weak landing and footwork | Same as above |
| Mostly **still photos** | Fast motion and blur are under-represented | Validate on your own 120–240 fps clips |
| **Broadcast/photo domain** | Domain shift to phone pickup film (✅ amateur single-camera paper) | Fine-tune later on your own labeled frames (Phase E) |

### 2.4 Basketball datasets (for learning structure, not ground truth for users)
BASKET ✅ (skill ratings from highlight reels; models far below humans) · BasketEvent ✅ (broadcast events tied to players) · PL-NBA ✅ (possessions; details UNKNOWN) · NBA SportVU 2015–16 ✅ (positions, which help with real closeout distances and speeds) · TrackID3x3 ◻ (confirm ID and license). Details and cautions: 02_research/resources.md §5.

---

## 3. Model selection matrix: YOLO11-pose vs. YOLO26-pose

### 3.1 Official numbers (✅ Ultralytics; COCO Keypoints val2017, 640 px, batch 1)

**YOLO11-pose** (✅ Ultralytics YOLO11 README; speeds measured on Amazon EC2 P4d, ONNX on CPU, TensorRT on T4)
| Model | mAP pose 50-95 | mAP pose 50 | CPU ONNX (ms) | T4 TensorRT10 (ms) | Params (M) | FLOPs (B) |
|---|---|---|---|---|---|---|
| YOLO11n-pose | 50.0 | 81.0 | 52.4 ± 0.5 | 1.7 ± 0.0 | 2.9 | 7.6 |
| YOLO11s-pose | 58.9 | 86.3 | 90.5 ± 0.6 | 2.6 ± 0.0 | 9.9 | 23.2 |
| YOLO11m-pose | 64.9 | 89.4 | 187.3 ± 0.8 | 4.9 ± 0.1 | 20.9 | 71.7 |
| YOLO11l-pose | 66.1 | 89.9 | 247.7 ± 1.1 | 6.4 ± 0.1 | 26.2 | 90.7 |
| YOLO11x-pose | 69.5 | 91.1 | 488.0 ± 13.9 | 12.1 ± 0.2 | 58.8 | 203.3 |

**YOLO26-pose** (✅ Ultralytics pose docs; released January 2026; mAP marked "e2e" = NMS-free head, `nms=False`; params/FLOPs are for fused models)
| Model | mAP pose 50-95 (e2e) | mAP pose 50 (e2e) | CPU ONNX (ms) | T4 TensorRT10 (ms) | Params (M) | FLOPs (B) |
|---|---|---|---|---|---|---|
| YOLO26n-pose | 57.2 | 83.3 | 40.3 ± 0.5 | 1.8 ± 0.0 | 2.9 | 7.6 |
| YOLO26s-pose | 63.0 | 86.6 | 85.3 ± 0.9 | 2.7 ± 0.0 | 10.4 | 24.1 |
| YOLO26m-pose | 68.8 | 89.6 | 218.0 ± 1.5 | 5.0 ± 0.1 | 21.5 | 73.3 |
| YOLO26l-pose | 70.4 | 90.5 | 275.4 ± 2.4 | 6.5 ± 0.1 | 25.9 | 91.7 |
| YOLO26x-pose | 71.6 | 91.6 | 565.4 ± 3.0 | 12.2 ± 0.2 | 57.6 | 202.3 |

**What changed in YOLO26** (✅ Ultralytics YOLO26 page): an optional NMS-free end-to-end head; DFL removed; new training recipe (MuSGD, Progressive Loss, STAL); for pose, **Residual Log-Likelihood Estimation (RLE)** for keypoint localization; vendor claims up to +7.2 AP on COCO pose vs. YOLO11 and up to 43% faster CPU for the nano model.

### 3.2 Side-by-side deltas (computed from the two tables)
| Size | Δ mAP 50-95 | Δ CPU ms | Δ T4 ms | Δ Params |
|---|---|---|---|---|
| n | **+7.2** | −12.1 (faster) | +0.1 | 0 |
| s | +4.1 | −5.2 (faster) | +0.1 | +0.5 |
| m | +3.9 | **+30.7 (slower)** | +0.1 | +0.6 |
| l | +4.3 | +27.7 (slower) | +0.1 | −0.3 |
| x | +2.1 | +77.4 (slower) | +0.1 | −1.2 |

### 3.3 How to read these honestly
- **These are vendor benchmarks on COCO photos.** Neither table says anything about jump shots, blur, or phone footage.
- **The CPU comparisons aren't apples to apples.** The YOLO11 table states EC2 P4d hardware; the YOLO26 page doesn't state the same setup, and its mAP is the "e2e" variant. Treat cross-table speed differences (especially "YOLO26 m/l/x slower on CPU") as **UNKNOWN until benchmarked on the same machine.**
- **The biggest real gain is at the small end:** YOLO26n gets +7.2 mAP at the same size. That matters if PTG ever runs on a phone.
- **For offline analysis, speed barely matters.** A 3-second clip at 240 fps is 720 frames. At ~40 ms per frame (n, CPU) that's ~29 s. At ~565 ms (x, CPU) it's ~7 minutes. On a T4, all sizes take seconds. **Pick by validity on PTG footage, not by speed.**

### 3.4 License matrix (this can decide the choice by itself)
| Option | License | What it means for PTG |
|---|---|---|
| Ultralytics YOLO11 / YOLO26 | **AGPL-3.0 or paid Enterprise** (✅ YOLO26 page) | Using it in a network service can require publishing **your app's source**, unless you buy Enterprise. That conflicts with the trade-secret and patent plan |
| MediaPipe Pose Landmarker ◻ | Apache 2.0 | Commercial-friendly; 33 landmarks including heel and foot index |
| RTMPose / MMPose ◻ | Apache 2.0 | Commercial-friendly; supports whole-body models (verify current models) |
| OpenCap ✅ | Research tool, cloud; two phones | Research validation, not a product dependency |
| Kinovea ◻ | Open source (verify license) | Used as a **standalone tool** for ground truth, so no linking concerns |

### 3.5 The PTG selection matrix (criteria and weights decided now; scores filled only after the bake-off)
| Criterion | Weight | How it's scored |
|---|---|---|
| **Event-frame error vs. Kinovea hand marks** (F1, F2, F4) on PTG clips | 35% | Median and 90th-percentile frame error |
| **Body-feature error** (F5, F6) vs. hand-calibrated measures | 20% | Mean absolute error in calibrated units |
| **Robustness** (blur, occlusion, lighting, left/right shooter) | 15% | % of reps with usable keypoints |
| **License fit** (product + IP plan) | 15% | Pass/fail first, then cost |
| **Keypoint coverage** (hands/feet available?) | 10% | Which features become possible |
| **Offline compute cost** | 5% | Minutes per session on your hardware |

**Candidates for the bake-off:** YOLO26n/s/m-pose, YOLO11m-pose (baseline), MediaPipe Pose, one RTMPose model. **No scores are written until they're measured.** Writing them early would be inventing numbers (P8).

---

## 4. Architecture: the CV sensor pipeline

### 4.1 Pipeline stages (runtime, conceptual)
```
 1 CAPTURE      protocol-controlled phone video (fps, angle, tape marks, consent)
 2 INGEST       read metadata: fps, resolution, duration; reject variable-frame-rate clips
 3 CALIBRATE    line calibration (vertical plane) + floor-plane calibration (tape grid)
 4 DETECT       person detection → pose keypoints per frame (+ confidence)
 5 BALL         ball detection per frame (+ confidence)
 6 TRACK        single-subject track (solo clips only in MVP)
 7 SMOOTH       temporal filter on keypoints (e.g., Savitzky-Golay / One Euro ◻), with parameters recorded
 8 EVENTS       candidate F1 catch, F2 release, F4 dip, landing, each with a ± frame window
 9 FEATURES     F3, F5, F6, F8 computed in calibrated units
10 QUALITY GATE drop reps with low confidence, blur, occlusion; log the reason
11 VALIDATE     compare to Kinovea marks (sampled); update the error bars
12 RECORD       save as DERIVED data with model name, version, parameters and error bars
```
Raw video and **human marks are never overwritten** (raw → reviewed → derived → generated).

### 4.2 What each stage hands to the next (contracts, in plain words)
| From → To | Sender guarantees | Receiver assumes | If it fails |
|---|---|---|---|
| Ingest → Calibrate | Constant fps, known resolution | Frame N = N/fps seconds | Reject the clip ("variable frame rate") |
| Calibrate → Features | Pixel-to-feet scale for a named plane, with error | Measurements lie on that plane | Feature marked "uncalibrated," not shown |
| Detect/Ball → Events | Per-frame keypoints/ball with confidence | Low-confidence frames are flagged | Event = "not found," never guessed |
| Events → Features | Event frame **and** its ± window | The window is honest | Feature carries the widened range (P15) |
| Quality gate → Record | Only reps passing the thresholds | — | Rep listed as unusable, with the reason |
| Record → Engine | Every number has version + error bars | Engine uses ranges | Engine says "inconclusive" (P9) |

### 4.3 Where the dev workspace (ICM) fits
The **workspace** holds the specs, validation reports and decisions. The **pipeline** is code (later, built by an agent). The folders below are for *thinking and validating*, not runtime:
```
cv/
├── CONTEXT.md                 router: which stage of CV work are we in?
├── capture-protocol.md        §8 below
├── event-definitions.md       F1–F10 exact definitions (frozen v1)
├── measurement-catalog.md     §1 table + status per feature
├── ground-truth/              Kinovea CSV exports + the double-marking ledger
├── bakeoff/                   per-model results (filled only after measuring)
├── validation-reports/        dated: agreement, error bars, decisions
└── decisions.md               model choice, thresholds, license decision
```

---

## 5. Kinovea integration workflows

Kinovea (✅ per its docs, as checked by your other chat: frame stepping, synchronized side-by-side, manual angle/distance/time measurement, semi-automatic point tracking, CSV export, line and floor-plane calibration; **time resolution = one frame**; **measurements valid only on a calibrated 2D plane**).

### Workflow A: build the ground truth (Phase A, now, no code)
1. Record per the capture protocol (§8).
2. In Kinovea: set **line calibration** using a known vertical length (e.g., a marked pole or the shooter's measured height) and **floor-plane calibration** using the tape grid.
3. Mark F1, F2, F4 and landing for every rep. Note the ± frame uncertainty when the event is ambiguous.
4. Export to CSV and paste into the ledger. **Know which export you used:** Kinovea's kinematics-dialog CSV uses **filtered** coordinates, while its spreadsheet export uses **unfiltered** coordinates (✅ per kinovea.org/help/en/export/data.html, as cited by your other chat). Event timestamps aren't affected; motion traces are. Record the export type in the ledger.
   - **Always log the original recording fps,** not the playback fps of a slow-motion export. Slow-mo exports can be re-timed and can hide the true frame rate.
5. **Re-mark everything a day later without looking.** Record the frame differences.
6. **Gate G1 (set before looking at data):** ≥90% of events agree within 2 frames. If not, fix the definitions or camera before going further.

### Workflow B: validate a model (Phase B, later)
1. Run each candidate model on the **same** clips.
2. For each event, compute model frame − human frame.
3. Report median, 90th percentile and worst case; plot model vs. human differences (a Bland-Altman-style plot ◻).
4. **Gate G2:** model-vs-human error ≤ human-vs-human disagreement **for that feature.** Pass → the feature can be automated. Fail → it stays manual.

### Workflow C: keep checking in production (Phase C onward)
- A random **5% of reps** get re-marked by hand every month.
- If the error grows (new phone, new gym, new lighting), the feature is **switched off automatically** until revalidated. This is the balancing loop that stops sensor drift.

### Workflow D: review for players (any phase)
- Side-by-side make vs. miss in Kinovea, synced at F1. This is the "SHOW" field of the mentor card, available on day one with zero code.

---

## 6. Validation plan and metrics

| Metric | For | Target (set before data; these are starting suggestions, labeled C) |
|---|---|---|
| Human test-retest agreement | F1, F2, F4 | ≥90% within 2 frames |
| Model frame error (median / 90th pct) | F1, F2, F4 | ≤ the human disagreement |
| Calibrated distance error | F5, F6 | Smaller than the smallest change PTG wants to detect |
| Usable-rep rate | All | Report it; a falling rate = a capture problem |
| **Review burden** | All automated events | % of events a human had to correct or mark unobservable. If most need rework, automation isn't saving anything |
| Margin test-retest | Release margin | Pass 1 vs. pass 2 margin difference, reported directly |
| Smallest worthwhile change | F3, F8 | From the player's own session-to-session noise (P9) |

**The margin only needs two marks.** Release margin = t_PTS(contest frame) − t_PTS(release frame), using each frame's **presentation timestamp** (see 03_design/05_cv-methods.md §4). For constant-frame-rate files this equals (f_contest − f_release) ÷ fps. **For variable-frame-rate phone files, only the timestamp version is correct.** **The catch frame cancels out.** So the core window measurement needs **no pose model at all**: just release and contest-proxy marks. Check the **margin's** repeatability directly (pass 1 vs. pass 2), because two marks that each agree within 2 frames can still disagree by up to 4 frames on their difference.

**Conditions never get pooled:** solo / simulated pressure (timer or visual cue) / observed live contest are three separate conditions. A timer is a drill deadline, not a measured defender.

**Data splits (for any later fine-tuning):** split train/validation/test by **recording session, and preferably by player**, never by adjacent frames of the same clip. Otherwise near-duplicate frames make the model look like it generalizes.

**Frame math to keep in mind:** at 60 fps one frame = 16.7 ms; at 120 fps 8.3 ms; at 240 fps 4.2 ms. An execution-time difference smaller than ~3 frames is noise until test-retest says otherwise.

---

## 7. PRD: the PTG Sensor Subsystem

**Problem:** players need honest numbers about their own reps (timing, dip, landing) without hand-marking every clip, but automated pose on phone video is unproven for basketball shooting.

**User:** an adult solo player filming practice with a phone on a tripod.

**Goal:** automate **only** the features that match hand marks, and show error bars on everything.

### Functional requirements
| ID | Requirement | Acceptance |
|---|---|---|
| FR1 | Reject clips without constant fps or required metadata | Rejected clip shows the reason |
| FR2 | Calibration step (line + floor plane) before distance features | Uncalibrated clips show timing only |
| FR3 | Detect F1/F2/F4/landing with a ± frame window | Every event has a window; none is guessed |
| FR4 | Compute F3, F5, F6, F8 only from passing events | Values carry ranges and versions |
| FR5 | Quality gate with logged reasons | Unusable reps listed, never silently dropped |
| FR6 | Store model name/version/parameters with each derived value | Any number can be traced to its model |
| FR7 | Export to CSV in the same shape as the Kinovea ledger | Direct comparison possible |
| FR8 | Per-feature kill switch tied to validation status | Failing features disappear from cards |

### Non-functional requirements
| ID | Requirement |
|---|---|
| NFR1 | Offline/after-session processing is acceptable; real-time is not required |
| NFR2 | Raw video stays on the user's device by default |
| NFR3 | License compatible with the product and IP plan (pass/fail before the bake-off) |
| NFR4 | Left- and right-handed shooters supported (flip-aware features) |
| NFR5 | Deterministic: same clip + same version = same numbers |

### Non-goals (MVP)
Automatic defender tracking · game-clip automation · joint-angle coaching · real-time feedback · gaze measurement · multi-player identity

### Risks
| Risk | Signal | Response |
|---|---|---|
| Release blur | F2 error large at 60 fps | Require 120+ fps; otherwise F2 stays manual |
| Wrist ≠ fingers | F1/F2 off by a few frames | Test a hands-capable skeleton, or keep manual |
| Domain shift (new gym/phone) | Usable-rep rate drops | Workflow C revalidation |
| AGPL conflict | Plan to ship as a service | Enterprise license or an Apache alternative |
| Precision illusion | Users trust decimals | Always show ranges; round to frame resolution |

---

## 8. Capture protocol (what makes the sensor possible)

| Setting | Spec | Why |
|---|---|---|
| Mount | Tripod, fixed, same spot every session | Calibration stays valid |
| View for body features | **Side-on (perpendicular to the shooting direction)**, about hip height | 2D sagittal measurement only works side-on |
| View for timing only | Front-quarter is OK | Timing is less view-sensitive than angles |
| Frame rate | **120–240 fps** for release; 60 fps minimum | Release blur and frame resolution |
| Shutter / light | Bright light; fast shutter if the phone allows | Less blur |
| Framing | Full body + ball + floor tape in frame for the whole rep | Keypoints and calibration visible |
| Calibration | Floor tape grid (e.g., 3 ft squares) + one known vertical length | Floor-plane + line calibration |
| Clothing | Fitted, contrasting with the background | Better keypoints |
| Reps | ≥10 per condition | Enough to estimate noise |
| Consent | Only you in frame for automated features | Bystander privacy; identity problem avoided |

---

## 9. Privacy and security for the sensor

- **Process on device; keep raw video local** unless the user opts in.
- **Bystanders:** blur faces (e.g., deface ◻) and crop others out. Blurring is a privacy measure, **not** legal compliance.
- **Keypoints may count as biometric data** under some laws (e.g., Illinois BIPA). Status: **UNKNOWN**, so get legal advice before storing them server-side.
- **Retention:** delete raw video after the ledger is built, unless the user keeps it.
- **No face recognition, no location data.**
- **Minors:** out of scope for MVP (COPPA).

---

## 10. Roadmap → moved
Sensor phases (A0 rehearsal → A ground truth → B bake-off → C solo pipeline → D game clips manual → E fine-tune → F production loop) are folded into [`06_roadmap.md`](06_roadmap.md) §3, Phases 0, 1 and 5. Their gates (G0–G5) stay in `05_cv-methods.md` §7.

---

## 11. Systems check on this blueprint

```
R (danger): better benchmark mAP → more trust → more features automated → more unvalidated numbers → wrong advice
B (guard):  Kinovea hand marks → error bars → kill switch → only validated features ship
Delay:      validation lags when the camera, gym or phone changes → monthly re-audit closes it
Trap:       "seeking the wrong goal" = optimizing COCO mAP instead of event-frame error on PTG clips
Leverage:   #6 information (error bars on every number) and #5 rules (no G2, no feature) beat #12 (picking n vs. x)
```

**The honest bottom line:** the model choice (YOLO11 vs. YOLO26, n vs. x) is a **#12 parameter decision.** The structural decisions are: the event definitions, the ground-truth loop, the kill switch, and the license. Get those right and any reasonable model can be swapped in. Get them wrong and the best model in the table will still produce confident fiction.

---

## 12. Open questions
1. What frame rate and resolution does your phone actually support at 120/240 fps?
2. Will PTG ever run as a network service? (This decides AGPL vs. Enterprise vs. Apache.)
3. Who is the second marker for test-retest on the ground truth?
4. Is landing displacement or dip timing more useful to you as a first automated feature?
5. Do you have side-on footage, or only front views?

---

## Appendix A: Five-clip annotation rehearsal (do this before the 20-rep session)

**Purpose:** fix the event definitions, not prove anything. After these five clips, revise the definitions and **freeze them (v1)** before the larger recording.

**Before annotating, write down:**
- Release definition (first frame with no finger-ball contact)
- Contest-proxy boundary (for example, "a box one ball-width around the ball in the image"), with a sketch
- What counts as "not observable"
- Original recording fps for each clip (from the file, not the slow-mo playback)

**Rules:** mark each clip twice, at least a day apart, in a **different order** the second time, without looking at pass 1.

| Clip | Condition (solo / simulated / live) | Original fps | Export (filtered / unfiltered / n/a) | Release P1 | Contest P1 | Margin P1 (s) | Release P2 | Contest P2 | Margin P2 (s) | Δ margin (frames) | Visibility notes |
|---|---|---:|---|---:|---:|---:|---:|---:|---:|---:|---|
| 01 |  |  |  |  |  |  |  |  |  |  |  |
| 02 |  |  |  |  |  |  |  |  |  |  |  |
| 03 |  |  |  |  |  |  |  |  |  |  |  |
| 04 |  |  |  |  |  |  |  |  |  |  |  |
| 05 |  |  |  |  |  |  |  |  |  |  |  |

Margin = PTS(contest frame) − PTS(release frame), with timestamps looked up via ffprobe (equals frames ÷ fps only if the clip is confirmed CFR; add a `cfr_or_vfr` column). Positive = released before the proxy. Solo and simulated clips have no contest proxy, so their margin is "n/a." Record release only.

**Predict first (judgment log):** before pass 1, write how many of the 5 clips you expect to agree within 2 frames on the margin.

**After the rehearsal, answer:** Which definition caused the most disagreement? Was anything "not observable" more than once? What changes in v1?

---

## 13. Sources (this document)
- Ultralytics pose task docs (YOLO26-pose table, keypoint format): https://docs.ultralytics.com/tasks/pose/
- Ultralytics YOLO11 README with pose table (Hugging Face): https://huggingface.co/Ultralytics/YOLO11/blob/main/README.md
- Ultralytics YOLO11 docs: https://docs.ultralytics.com/models/yolo11/
- Ultralytics YOLO26 docs (changes, RLE, license): https://docs.ultralytics.com/models/yolo26/
- **Primary paper (✅ exists):** Jocher et al., "Ultralytics YOLO26: Unified Real-Time End-to-End Vision Models," arXiv:2606.03748 (June 2026), https://arxiv.org/abs/2606.03748. Another chat reports the pose figures come from its Table S10 (E2E variants, COCO val, 640 px); I confirmed the paper, not the table. Note that most of the "+7.2 AP" gain is the nano model (50.0 → 57.2); other sizes gain +2.1 to +4.3
- Ultralytics COCO-Pose dataset page (splits, keypoint order, flip index, license): https://docs.ultralytics.com/datasets/pose/coco/
- Alternate YOLO11 table mirrors: https://huggingface.co/Ultralytics/YOLO11 · https://app.alphaneural.io/models/Ultralytics/YOLO11 · Italian pose page (possibly stale values): https://docs.ultralytics.com/it/tasks/pose/
- Amateur single-camera video paper: https://www.emergentmind.com/papers/2609.28049
- Kinovea: https://kinovea.org/
