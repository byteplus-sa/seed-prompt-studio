# Motion Review Prompt — deep frame-by-frame pass

A static "action" description is not enough to reproduce how a template
moves. Run this as a **second pass** on the same source (agent video pass, or
an external video-capable tool). Feed the existing shot list and the SHA-256
of the approved breakdown revision with the prompt. This is the primary fix
for "our version looks static".

```text
You are a senior motion designer and video analyst. Analyze the attached video
frame by frame and report, exhaustively, how it MOVES.

Use the supplied shot boundaries without renumbering them. For EACH shot report:
1. time range
2. what is in the shot (subjects, background elements, effects)
3. every moving element with its motion type (translate/rotate/scale/parallax/
   drift/float-bob/pulse/flicker/color-cycle/twinkle/shimmer/sway/wave/slide/
   zoom/rotation), direction, speed, amplitude, easing, and loop period
4. camera motion (static/dolly/push/pan/tilt/rotate/shake) — be explicit when
   the camera is truly static and only elements move
5. light/color motion (pulse, strobe, flicker, hue cycle, blink, bloom,
   shimmer) and its rate
6. the single strongest motion cue that makes the shot feel alive

Separate direct observation from uncertain estimates. Give each shot a
low/medium/high confidence and list uncertainty whenever confidence is not
high. Then list the top concrete imperative prompt wording (positive only —
say what moves and how, never negative phrasing) to reproduce the same
movement in a Seedance 2.5 prompt.

Return JSON only, conforming to motion-review-schema.json. Key every result by
shot_index.
```

## Working from keyframes

When only keyframes are available, motion must be inferred. Mark every
inferred motion as an estimate, set confidence to low unless the frames
clearly bracket a change, and say so in the review. Ask the user to confirm
or correct the strongest cues before the Seedance prompts depend on them.

## Merge contract

- Write the exact JSON output beside the breakdown as `motion-review.json` and
  a readable rendering as `motion-review.md` when the user requests saved
  drafts.
- Merge per-shot motion **by `shot_index`, never by array position**, into the
  analysis as `shots[].motion` (fields: `camera_motion`, `moving_elements[]`,
  `light_motion`, `strongest_cue`).
- Bind the approved breakdown revision's SHA-256 as the motion review's
  `source_breakdown_sha256`.
- The imperative wording fills the Seedance Action slot; it is **prompt text**
  and must follow positive-only directing principles and pass the
  `prompt-review` gate.
- Preserve the approved breakdown revision; merge valid motion into a new
  revision. If motion review proposes changed timing or action, re-present the
  affected decisions rather than silently changing an approved breakdown.
