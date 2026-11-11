---
type: CV methods spec (architecture detail, design only, no code)
project: PTG (Practice-to-Game Lab)
status: draft v0.1
updated: 2026-11-10
extends: 03_design/04_cv-blueprint.md (benchmark tables, sensor PRD, Kinovea workflows, Appendix A ledger)
governed by: 01_foundation/foundation.md (P8, P9, P12, P15)
tags: ✅ confirmed from a source this session · ◻ standard practice / well-known, verify · UNKNOWN = no evidence found
---

# PTG CV Methods: RLE, Multi-Camera Sync, Joint Angles, Variable Frame Rate, Labeling QC

## 0. Where this sits

```
CAPTURE ─► TIMING (VFR-safe, §4) ─► POSE (model matrix §1, RLE §2) ─► [MULTI-CAM SYNC §3, later]
        ─► ANGLES (§5) ─► QUALITY GATE ─► VALIDATION against LABELS (§6) ─► record with error bars
```
**MVP scope reminder:** one phone, solo clips, timing first. Multi-camera (§3) and joint angles (§5) are designed here so later phases don't improvise, but **both stay out of the MVP** until the gates in §6 pass.

---

## 1. Comparison matrix: YOLO11-pose vs. YOLO26-pose (extended)

The full per-size tables (COCO mAP50-95, mAP50, params, FLOPs, CPU ONNX, T4 TensorRT10) live in 03_design/04_cv-blueprint.md §3 (✅ official Ultralytics pages). Summary:

| Size | YOLO11 mAP50-95 | YOLO26 mAP50-95 (e2e) | Params 11 → 26 (M) | FLOPs 11 → 26 (B) | CPU ONNX 11 → 26 (ms) | T4 TRT10 11 → 26 (ms) |
|---|---|---|---|---|---|---|
| n | 50.0 | 57.2 | 2.9 → 2.9 | 7.6 → 7.6 | 52.4 → 40.3 | 1.7 → 1.8 |
| s | 58.9 | 63.0 | 9.9 → 10.4 | 23.2 → 24.1 | 90.5 → 85.3 | 2.6 → 2.7 |
| m | 64.9 | 68.8 | 20.9 → 21.5 | 71.7 → 73.3 | 187.3 → 218.0 | 4.9 → 5.0 |
| l | 66.1 | 70.4 | 26.2 → 25.9 | 90.7 → 91.7 | 247.7 → 275.4 | 6.4 → 6.5 |
| x | 69.5 | 71.6 | 58.8 → 57.6 | 203.3 → 202.3 | 488.0 → 565.4 | 12.1 → 12.2 |

**Beyond the benchmark numbers:**
| Dimension | YOLO11-pose | YOLO26-pose | PTG note |
|---|---|---|---|
| Keypoint method | Standard Ultralytics pose head | **RLE** integrated for keypoint localization ✅ | §2: impact on occluded limbs is **UNKNOWN** |
| NMS | Required | Optional NMS-free end-to-end head ✅ | Benchmark "e2e" ≠ default path. Test the exact path you'll ship |
| Skeleton | COCO-17 | COCO-17 | Neither has ball, fingers or heel/toe |
| Per-keypoint confidence | Exposed | Exposed | Whether YOLO26 exposes an RLE **uncertainty (σ)** separately is **UNKNOWN** |
| License | AGPL-3.0 / Enterprise | AGPL-3.0 / Enterprise ✅ | Pass/fail for the IP plan; include an Apache option in the bake-off |
| Benchmark comparability | EC2 P4d stated | Hardware stated differently | Cross-table CPU deltas = UNKNOWN until same-machine test |

---

## 2. RLE (Residual Log-Likelihood Estimation) and occluded limbs

### What RLE is (✅ Li et al., ICCV 2021, oral)
- Most keypoint **regression** models use a simple loss (L1/L2), which quietly assumes a fixed error shape.
- RLE **learns the shape of the error distribution** with a normalizing flow (RealNVP), modeling the *residual* change from a simple base distribution so training stays stable.
- The paper reports **+12.4 mAP on COCO** for regression with RLE, with **no extra cost at test time** (the flow is only used in training).
- Typical RLE heads predict a **location (μ)** and a **scale (σ)** per keypoint. The supplement notes one log-scale term in the loss wasn't necessary. **How Ultralytics implements and exposes this in YOLO26 is UNKNOWN.**

### What RLE does and doesn't promise for basketball
| Question | Evidence | Status |
|---|---|---|
| Better average keypoint accuracy on COCO? | Paper + Ultralytics claim up to +7.2 AP for YOLO26 over YOLO11 | ✅ on COCO only |
| More accurate **occluded** limbs (arm behind the head, hand behind the ball)? | No source found | **UNKNOWN** |
| **Better-calibrated uncertainty**, so occluded points get honestly low confidence? | Plausible from how RLE works; not shown for YOLO26 | **HYPOTHESIS** |
| Fixes motion blur at release? | No | Not what RLE addresses |

**The systems reading:** for PTG, the valuable part of RLE isn't a few extra mAP points. It's the possibility of an **honest per-keypoint uncertainty**, which feeds the quality gate and error bars directly (P8). That's a hypothesis to test, not a feature to assume.

### Occlusion test protocol (run during the bake-off)
1. Label a **visibility-stratified set**: each keypoint tagged visible / occluded-but-inferable / not visible (§6).
2. For each model: report keypoint error **separately** for visible vs. occluded points (normalized by torso length or box size).
3. **Calibration check:** bin keypoints by model confidence; in each bin, measure the actual error. A calibrated model's high-confidence bins have small errors and its low-confidence bins have large ones.
4. **Decision rule (set before data):** use a model's confidence as the quality gate only if its confidence is calibrated on occluded points. Otherwise gate on human visibility labels during validation and keep occluded features manual.
5. Basketball occlusion hot spots to oversample: **shooting hand behind the ball at set point, guide hand, far-side arm in a side view, feet at landing, any defender overlap.**

---

## 3. Multi-camera tracking and sync pipeline (Phase E+, not MVP)

**Why it's needed at all:** one phone measures only one 2D plane (P8). True 3D joint angles need **≥2 synchronized, calibrated views** (OpenCap ✅ uses two phones). Phones have no shared clock and no genlock, so sync is the hard part.

### 3.1 Pipeline
```
1 PLAN       camera positions (~60–90° apart), same fps setting, fixed exposure
2 INTRINSICS each phone: checkerboard calibration (focal length, lens distortion) ◻
3 EXTRINSICS where the phones are relative to the court: known floor points / tape grid / a wand ◻
4 RECORD     a SYNC EVENT at start AND end of every take (see 3.2)
5 TIMESTAMPS extract per-frame presentation timestamps (PTS) for each camera (§4)
6 ALIGN      estimate the time offset (and drift) between cameras from the sync events
7 RESAMPLE   map both streams onto one shared timeline by TIMESTAMP (interpolate keypoints, never frames)
8 TRACK      associate the same person across cameras (solo clips: trivial; multi-player: hard)
9 TRIANGULATE 2D keypoints → 3D (DLT / least squares) ◻, per keypoint, only where both views are confident
10 GATE      reprojection error per keypoint; reject points above the threshold
11 RECORD    3D points + reprojection error + sync residual + calibration version
```

### 3.2 Sync methods (cheapest first)
| Method | How | Resolution | Caution |
|---|---|---|---|
| **Visual event** | A sharp, visible event in both views (hand clap, ball slammed on the floor, flash/LED) | ~1 frame per camera | The event must be visible to both; mark it in both clips |
| **Audio cross-correlation** | Align the clap waveform across both files | Sub-frame possible | Audio and video tracks in phone files can have their own offset ◻ |
| **Motion cross-correlation** | Align a shared movement signal (e.g., hip vertical velocity) | ~1 frame | Needs good keypoints first (chicken-and-egg) |
| **Start + end events** | Two sync events per take | Estimates **clock drift** too | Required for takes longer than a few seconds |

**Clock model (offset + drift):** map each camera's timestamps to one global clock with `t_global = a·t_PTS + b` (b = offset, a = clock-rate correction), fit on the start and end sync events, and record the residual. Match frames across cameras **by nearest timestamp**, keeping each original timestamp and the mismatch. Never duplicate frames to fake alignment.

**Cross-camera identity:** keep track IDs camera-local first. Associate views using time, court position, geometry (epipolar consistency ◻) and appearance; expose uncertain matches for manual correction.

**Gates:** sync residual within a tolerance **declared per analysis before data** (starting suggestion, C: ≤ half a frame at the lower fps; faster movements need tighter); reprojection error under a threshold set in the pilot; drift between start and end events reported. Fail → no 3D numbers for that take.

**Other physics to respect:** rolling shutter (fast arm and ball movement skews within a frame ◻), different fps between phones (resample by time, §4), and auto-exposure changes (lock exposure).

---

## 4. Handling variable frame rate (VFR), required from day one

### 4.1 Why it matters
Phones often record **variable frame rate**: the time between frames changes (heat, light, processing). Then **"frame number ÷ fps" is wrong**, and so is every margin, execution time and velocity built on it. Summary fields can mislead: an "average fps" can't recover per-frame intervals, a "constant" label doesn't guarantee clean timestamps, and some files report inflated rates (e.g., Android files showing 120/240 when the true rate is 30) ✅.

### 4.2 The rule
> **Time comes from each frame's presentation timestamp (PTS), never from frame number ÷ fps.**

Updated margin (replaces the frames ÷ fps version):
```
release margin M = t_PTS(contest-proxy frame) − t_PTS(release frame)
```
For truly constant-frame-rate files the two formulas agree. For VFR files, only the PTS version is correct.

### 4.3 Protocol
| Step | Action | Tool |
|---|---|---|
| 0. Identify | Store a **file hash** of every original, plus camera ID, codec, dimensions, nominal fps and timebase | Hash tool |
| 1. Inspect | For every original file, list **per-frame timestamps**; flag **missing, repeated or non-monotonic** timestamps (e.g., `best_effort_timestamp_time`, `pkt_duration_time`) ✅ | ffprobe |
| 2. Classify | Compute the frame-to-frame intervals: their median, spread, gaps and repeats. Label the clip **CFR** if the spread is within a tolerance set in the pilot, else **VFR** | Spreadsheet |
| 3. Keep originals | Never analyze a re-exported or slow-mo-rendered copy. Log the **capture** rate, not the playback rate | — |
| 4. Mark | Mark events by frame in Kinovea, then **look up each marked frame's PTS** from the ffprobe list | Kinovea + ffprobe |
| 5. Don't "fix" first | Converting VFR → CFR duplicates or drops frames and can shift events. If a CFR copy is ever needed for display, keep both and map frames back to original PTS ✅ (measure before normalizing) | ffmpeg |
| 6. Clock test | Once per phone/setting: film a fast, known timer or blinking LED and check that timestamp intervals match reality ◻ | Phone + timer |
| 7. Record | Ledger columns: `capture_fps_claimed`, `cfr_or_vfr`, `interval_median_ms`, `interval_spread_ms`, `pts_release`, `pts_contest` | Ledger |

**Kinovea and VFR:** whether Kinovea's time display uses true per-frame timestamps or assumes constant fps is **UNKNOWN**. Until checked, trust **frame indices from Kinovea + times from ffprobe.**

**Prevention:** some phone camera modes or pro camera apps can lock frame rate and exposure ◻. Test which settings on your phone give CFR files, then put them in the capture protocol.

### 4.4 Velocity with VFR
Any rate (angular velocity, hip speed) uses the **actual time step**:
`v_i = (x_{i+1} − x_{i−1}) / (t_{i+1} − t_{i−1})` using PTS times, never a constant Δt.

---

## 5. Joint-angle calculation (methods; research-only until validated)

### 5.1 Definitions
- **Joint angle** (three points): the angle at joint B formed by points A–B–C. Examples: elbow = shoulder–elbow–wrist; knee = hip–knee–ankle; hip = shoulder–hip–knee.
- **Segment angle** (two points vs. a reference): e.g., forearm angle vs. vertical, or trunk lean vs. vertical. **Often more robust** than three-point angles in 2D.

### 5.2 The math
```
u = A − B,  v = C − B
unsigned angle  θ = atan2( |u × v| , u · v )          (0°–180°, numerically stable)
signed 2D angle θs = atan2( u_x·v_y − u_y·v_x , u · v )  (direction of bend)
segment angle   φ = atan2( Δx , Δy ) vs. the image vertical (after camera-tilt correction)
```
Use `atan2`, not `acos(dot/(|u||v|))`, which is unstable near 0° and 180°. In 3D (after triangulation, §3), the same formulas use 3D vectors, and the bend direction comes from a body-fixed reference, not the image.

### 5.3 Processing order
0. **Compute geometry in pixel units, never in normalized coordinates.** Normalized x is divided by image width and y by image height. On a non-square frame (e.g., 1920×1080) that stretches one axis and **changes every angle**. Convert normalized → pixels first (x·width, y·height), or to calibrated units.
1. **Confidence gate:** drop keypoints below the validated threshold (§2 calibration).
2. **Gap fill:** interpolate gaps ≤ a small, preset number of frames (by timestamp); longer gaps stay missing. Never invent values.
3. **Filter coordinates, not angles.** ⚠ Standard digital filters (Butterworth included) **assume evenly spaced samples.** For VFR clips, either resample to a uniform time grid first (documented, with a map back to the original timestamps) or use a time-aware smoother. Never filter across long gaps as if they were neighboring frames. Then a zero-lag low-pass filter (e.g., 4th-order Butterworth run forward and backward ◻). Choose the **cutoff by residual analysis** (Winter's method ◻), not a copied number. Fast shooting motions likely need a higher cutoff than walking. Record the cutoff.
4. **Compute angles** from the filtered points.
5. **Derivatives** (angular velocity) with VFR-safe time steps (§4.4).
6. **Report** each angle with: view check result, keypoint confidences, filter settings, and the validated error band.

### 5.4 The 2D error you can't filter away
- 2D angles are **projections**. If the limb moves out of the plane facing the camera, the measured angle is wrong, sometimes badly.
- **View check:** an angle is reported only if the camera is roughly perpendicular to the plane of motion (side-on for knee and elbow in the shooting plane) and the body isn't rotated more than a preset amount (estimated from shoulder/hip width shrinking in the image).
- Markerless validity is **joint- and task-specific** (shelf 2b); several-degree errors are common. A make-vs-miss difference smaller than the error band is **not reported**.

### 5.5 Validation of angles
- **Reference:** Kinovea manual angle tool on the same frames. That's also 2D and also has human error, so first measure the human test-retest error.
- **Gate:** model angle error ≤ human test-retest error, per joint, per view. Fail → that angle stays out of the product.
- **Better reference later:** two-phone 3D (OpenCap or §3) to quantify how wrong single-view 2D is for jump shots specifically.

---

## 6. Data labeling and quality-control protocol (draft)

### 6.1 What gets labeled
| Label type | Content | Tool | Format |
|---|---|---|---|
| **Events** | Catch, release, dip, landing, contest proxy (+ "not observable") | Kinovea (frame index) + ffprobe (PTS) | CSV ledger |
| **Keypoints** | COCO-17 (x, y, visibility) on sampled frames | CVAT skeleton annotation ◻ | COCO JSON |
| **Ball** | Box or center on sampled frames | CVAT | COCO JSON |
| **Clip metadata** | Condition, phone, capture fps, CFR/VFR, view, lighting, fatigue 1–5, consent ID | Ledger | CSV |
| **Quality codes** | BLUR · OCCL · OUT_OF_FRAME · AMBIG_CONTACT · LIGHT · VFR_GAP · OTHER | Both | Enumerated field |

**Storage rule:** keep pose labels COCO-compatible, and put everything COCO has no field for in a **sidecar record**: clip/camera ID, PTS and timebase, track ID and identity status, events and definition version, occlusion source (self / other player / ball / edge / unknown), truncation, blur, calibration and sync version, annotator/reviewer/date. Don't force extra meaning into pose fields. Automated checks on every export: keypoint count and order, coordinate range, box validity, visibility values, left/right flip consistency.

### 6.2 Roles
- **Annotator:** labels per the guide.
- **Reviewer:** checks a sample and flags errors.
- **Adjudicator:** resolves disagreements and updates the guide (you, plus a coach for event definitions).

### 6.3 The annotation guide (frozen, versioned)
- Exact definitions (events, keypoint placement, visibility 0/1/2, contest-proxy boundary), with **pictures of edge cases**.
- What to do when unsure: mark "not observable" or the quality code. **Never guess.**
- Change log: every change gets a version number, a date and a reason. Labels record which guide version they used. A change that affects existing labels triggers re-labeling of those items.

### 6.4 Bias controls
- **Blind to outcome:** trim clips so the annotator **can't see whether the shot went in** when marking catch/release. Knowing the result can pull the marks.
- **Blind to condition when possible:** hide whether the clip is "pre" or "post" lesson.
- **Randomize order**, and use a different order on the second pass.
- **No model pre-labels during the pilot.** Pre-labels anchor humans to the model's mistakes. Later, if used, audit pre-labeled vs. from-scratch agreement.

### 6.5 Qualification and gold set
- **Gold set:** 20–30 items labeled by consensus (you + a coach), covering the edge cases.
- **Qualification:** a new annotator labels the gold set first; they pass if they meet thresholds set before testing.
- **Hidden gold:** ~5% of each batch are gold items, used to catch drift.

### 6.6 Agreement metrics
| Label | Metric | Starting target (C: set before data, revise after the rehearsal) |
|---|---|---|
| Events | Frame/time difference between passes and annotators; % within 2 frames | ≥90% within 2 frames |
| Margin | Pass-to-pass margin difference (direct) | Reported; feeds P9 noise band |
| Keypoints | Per-keypoint pixel error normalized by torso length; annotator-vs-annotator OKS ◻ | Per joint, stratified by visibility |
| Visibility flags | Cohen's kappa ◻ | Report; low kappa → clarify the guide |
| Quality codes | % agreement on code assignment | Report |
| Continuous measures | ICC (test-retest) ◻ | Report with confidence interval |

### 6.7 Double-labeling rate
- **Pilot (Appendix A rehearsal + first 20 reps):** 100% double-labeled.
- **After targets are met:** 20% double-labeled, randomly chosen, plus all items with quality codes.
- **If agreement drops below target in any batch:** back to 100% until fixed.

### 6.8 Adjudication
- Differences within tolerance → average (events) or keep the reviewer's label (keypoints).
- Beyond tolerance → adjudicator decides with the guide. If the guide doesn't cover it, the guide gets updated (new version).
- Every adjudication is logged: item, both labels, decision, reason, guide version.

### 6.9 Versioning and lineage
Every label stores: item ID, clip ID, annotator ID, pass number, guide version, tool and version, timestamp, export type (filtered/unfiltered for Kinovea), and for events, both frame index and PTS. **Labels are never overwritten.** Corrections are new versions.

### 6.10 Splits (for any training or evaluation)
- Split by **session, and by player when possible**. Never put frames from the same clip in both training and test.
- A **locked test set** that nobody looks at while tuning, used once per decision.

### 6.11 Ongoing QC loop
```
Each batch:   hidden-gold accuracy + double-label agreement → pass? → accept batch
Monthly:      re-label an anchor set → drift check
Any change:   new phone / gym / guide version → mini re-validation before mixing data
Dashboard:    agreement by label type · quality-code rates · unobservable rate · review burden
```

### 6.12 Privacy in labeling
Consent ID required per clip; bystanders blurred before labeling; labeling done on local or access-controlled storage; no clips leave the project without the player's permission; delete per retention policy.

---

## 7. Updated gates (all phases)

| Gate | Passes when | Unlocks |
|---|---|---|
| G0 timing | Clip classified CFR/VFR; PTS extracted; clock test passed for that phone | Any timing measurement |
| G1 human | ≥90% events within 2 frames, and margin test-retest reported | Ground truth |
| G2 model | Model event error ≤ human disagreement, per feature | Automating that feature |
| G3 occlusion | Model confidence calibrated on occluded points (§2) | Using confidence as the quality gate |
| G4 angle | Model angle error ≤ human test-retest error, per joint and view | Showing that angle (research → product) |
| G5 3D | Sync residual ≤ ½ frame; reprojection error under threshold | Any 3D number |

---

## 8. Systems check

- **The precision trap, again:** RLE, triangulation and Butterworth filters make numbers *look* rigorous. Every one of them is useless if the timestamps are wrong (§4) or the labels are inconsistent (§6). **The order of leverage: timing truth → label truth → model choice → fancy math.**
- **Delays:** labeling is the slowest loop. Plan the labeling capacity (your hours per week) before planning model work. That's the real bottleneck (a #11 buffer).
- **Balancing loops built in:** hidden gold (catches annotator drift), monthly anchors (catches tool drift), kill switches (catch model drift).

---

## 9. Sources (this document)
- Li et al., "Human Pose Regression with Residual Log-Likelihood Estimation," ICCV 2021: https://openaccess.thecvf.com/content/ICCV2021/html/Li_Human_Pose_Regression_With_Residual_Log-Likelihood_Estimation_ICCV_2021_paper.html · arXiv: https://arxiv.org/abs/2107.11291 · supplement: https://openaccess.thecvf.com/content/ICCV2021/supplemental/Li_Human_Pose_Regression_ICCV_2021_supplemental.pdf · code: https://github.com/Jeff-sjtu/res-loglikelihood-regression
- VFR timestamps and ffprobe per-frame fields: https://josephnilo.com/blog/variable-vs-constant-frame-rate/ · https://assets.transloadit.com/glossary/variable-frame-rate/ · https://macmyths.com/how-to-validate-video-metrics-with-frame-by-frame-checks/
- Android files reporting inflated fps: https://forum.shotcut.org/t/some-android-variable-fps-videos-are-detected-as-120-240fps-instead-of-30/45610
- ffprobe frame-rate rounding bug: https://fftrac-bg.ffmpeg.org/ticket/9390 · FFmpeg VFR timestamp thread: https://ffmpeg.org/pipermail/ffmpeg-user/2018-September/041423.html
- Ultralytics YOLO26 / pose / YOLO11 tables: see 03_design/04_cv-blueprint.md §13
