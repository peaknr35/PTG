---
type: event definitions
version: v0 (rehearsal only; revise into v1 after the debrief, then freeze)
created: 2026-11-10
---

# Event Definitions v0

Mark each event by **Kinovea frame number**. If it can't be seen clearly, write **NO** (not observable) and add a quality code.

## Events

### CATCH (optional in this rehearsal)
**First frame where a hand visibly touches the ball on the pass.**
- If the hand is hidden behind the ball or the body: NO.

### RELEASE (required)
**First frame where no finger is touching the ball.** In other words, the frame where a gap first appears between the fingertips and the ball.
- Step one frame at a time near release. If the ball and fingers are a blur, write NO + `BLUR`.

### CONTEST PROXY (live clips 04–05 only)
**First frame where any part of the defender's hand comes within one ball-width of the ball, as seen in the image.**
- "One ball-width" = the ball's diameter on screen in that frame. Imagine a ring around the ball one ball-width wide; the first frame the hand touches that ring.
- It's an **image-based proxy**, not proof the shot was "contested" in 3D. The defender may be closer to or farther from the camera than the ball.
- If the hand never gets that close before release: write `NONE` (that's an answer, not a missing mark).
- If the hand or ball is hidden at the key moment: NO + `OCCL`.

### CUE (simulated clip 03 only)
**First frame of the audible beep or the partner's "go."** Use audio if Kinovea shows it; otherwise a visible signal. It's a **drill deadline**, not a defender, so it never goes in the contest column.

## Quality codes
`BLUR` motion blur · `OCCL` hidden by body/ball/person · `OUT` out of frame · `AMBIG` can't tell contact · `LIGHT` too dark/bright · `VFR` timestamp gap or repeat near the event · `OTHER` (explain in notes)

## Margin
```
margin (s) = time(CONTEST frame) − time(RELEASE frame)
positive  → released before the hand reached the ring
negative  → the hand reached the ring first
```
- `time()` comes from the timestamp file (PTS). If PTS wasn't checked: frames ÷ fps, and mark the row **"assumes CFR."**
- Solo and simulated clips: margin = n/a.
- **A negative margin does NOT mean the shot was a bad decision.** It's just the order of two events on video.

## Known weak spots (watch these and write them down)
- Release through blur at 30–60 fps
- The ring when the defender is far from the ball in depth
- Which hand counts if the defender uses both
- Clips where the ball leaves the frame
