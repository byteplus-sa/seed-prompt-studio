---
name: seedance-graybox-world
description: >-
  Write Seedance 2.5 structured edit prompts for Blender-rendered source videos —
  viewport/OpenGL playblasts, graybox and previz renders, solid-shading previews,
  and finished Eevee/Cycles renders. Change appearance — materials, textures,
  lighting, environment, atmosphere, style — while preserving the Blender-authored
  geometry, motion, camera path, timing, and cuts exactly. Use when the user
  supplies a Blender video (or its frames) and wants it finished, restyled,
  relit, or re-surfaced. Not for new text-to-video shots (seedance-prompt-25)
  or general non-Blender footage edits (seedance-vfx-prompt).
---

# Seedance Graybox World

Edit Blender-rendered videos with Seedance 2.5 structured video editing: the
Blender clip is `@Video 1`, the prompt changes how it looks, and everything
Blender authored — geometry, motion, camera, timing — stays locked.

Prompt-composition only — this skill emits prompt text; it never calls models.

## What this skill edits

A Blender-rendered source video, in any of its preview or final forms:

| Blender source | What it looks like | Typical edit goal |
| --- | --- | --- |
| Viewport / OpenGL playblast | Flat gray or solid shading, no materials, no anti-aliasing, viewport grid and overlays visible | Materialize, light, and place it in an environment — the finished look without a re-render |
| Graybox / previz render | Untextured gray geometry, ambient occlusion only | Same: finish the look while keeping the blocked motion |
| Solid-shading preview | One flat material on every surface | Assign real materials, textures, and lighting |
| Finished render (Eevee / Cycles) | Full materials and lighting | Restyle, relight, change time of day or weather, replace the environment |

The Blender clip is the source of truth for motion. The edit is a
**re-surfacing**, not a remake: never re-block, re-time, or re-stage what
Blender already authored.

## When to use / not use

Use when the user supplies a Blender video (or frames from one) and wants its
appearance changed: finish a playblast, surface a graybox, restyle or relight a
render, swap its environment.

Do **not** use for:

- new text-to-video or image-to-video shots (use `seedance-prompt-25`)
- general edits on non-Blender footage (use `seedance-vfx-prompt`)
- still images, character sheets, or location plates (use the Seedream skills)
- exact typography, UI, logos, or overlays — out of scope in this workspace

## The change contract

State both sides explicitly in every prompt.

| Blender owns — must preserve | Seedance owns — may change |
| --- | --- |
| Geometry, proportions, object count, silhouettes | Materials, textures, surface finish |
| Motion, performance, physics timing | Lighting direction and quality |
| Camera path, framing, lens, cuts | Background, environment, set dressing |
| Event order and shot timing | Atmosphere, weather, color grade, style |

Never re-describe the scene from scratch — that tells the model to ignore the
source. Describe only the appearance change and lock the rest.

## Source analysis modes

Inspect the Blender clip before writing: run an **agent video pass** when your
client can watch the video; otherwise extract frames locally (`ffprobe` for
duration and fps; `ffmpeg` for per-shot frames and short dense bursts across a
motion-critical window) and read them as images. Never claim to have watched
footage you could not access — sampled motion is an inference, not a
measurement.

## Canonical prompt structure

```text
[Edit Goal]
Edit @Video 1. <one-sentence appearance change: surface / relight / restyle /
replace the environment>.

[Source Video Role]
@Video 1 is the sole editing master — a Blender <playblast / graybox previz /
solid-shading preview / finished render>. It defines the geometry, motion,
camera path, framing, timing, and cuts.

[Target Material Role]           (only when a reference defines the target)
@Image 1 defines only <the target appearance>. Do not use its composition,
people, or camera.

[Edit Scope]
Change only <appearance categories: materials, lighting, environment>. Keep every
object, its geometry, motion, camera, framing, and timing from @Video 1. Exactly
one <subject> remains in frame — never a second or duplicated copy. Do not modify
<content to preserve>.

[Content to Preserve]
Keep the geometry, object count, motion, camera path, framing, cuts, and event
timing from @Video 1 unchanged. <Grounding and face-protection lines when people
are visible.>
```

Deliver with the parameter block: `omni_reference_task_type="edit"`, `generate_audio: false` when the
source is silent (Blender previews usually are), `resolution` 480p/720p/1080p.
Duration auto-locks to the source (±0.3s) — do not set it. If the source exceeds
30s, ask the user to trim it to ≤29s before you write the prompt; if it exceeds
one clip, ask the user to split it on shot boundaries.

## Recipes

### A. Playblast / graybox → finished look

The core case: replace the preview shading with real appearance while keeping
the blocked motion.

```text
[Edit Goal]
Edit @Video 1. Surface the untextured Blender preview with <real materials,
textures, and lighting> so it reads as <target look>, keeping every object,
motion, camera move, and cut exactly as they are.

[Source Video Role]
@Video 1 is the sole editing master — a Blender playblast with flat gray preview
shading. It defines the geometry, the motion of every object, the camera path,
framing, timing, and cuts.

[Edit Scope]
Change only appearance: assign <materials and textures> to the existing geometry,
light the scene with <key direction and quality>, and set the background to
<environment>. Do not move, add, or remove any object; do not change the camera
path, framing, timing, or cuts. Exactly one <subject> remains in frame — never a
second or duplicated copy. Remove the viewport grid, gizmos, and overlay lines —
they are preview artifacts, not content.

[Content to Preserve]
Keep the geometry, object count and placement, motion, camera path, framing, cuts,
and event timing from @Video 1 unchanged. <Face protection when people appear.>
```

### B. Restyle or relight a finished render

Change mood, time of day, palette, or light direction; keep the render's
composition and motion.

```text
[Edit Goal]
Edit @Video 1. Relight the scene to <new lighting: direction, quality, time of
day> and grade it to <palette or mood>, keeping every object, motion, camera
move, and cut exactly as they are.
```

State the light change physically (direction, softness, color temperature) and
what stays fixed (`key light direction`, `exposure`, `framing`). Do not stack a
lighting preset on top of a grade that fights it — one dominant look per clip.

### C. Background / environment replacement

Swap what surrounds the Blender animation without touching the animation.

```text
[Edit Goal]
Edit @Video 1. Replace only the background with <new environment>, keeping the
subject, its motion, the camera path, framing, timing, and cuts exactly as they
are. Match the new environment's light direction to the subject's existing key
light; ground the subject naturally — no cut-out edge, no halo.
```

### D. Preview cleanup (minimal edit)

Remove viewport artifacts and sharpen the preview shading without changing the
look's intent.

```text
[Edit Goal]
Edit @Video 1. Remove the viewport grid, gizmos, selection outlines, and overlay
lines, and clean the flat preview shading into even, anti-aliased surfaces,
keeping geometry, motion, camera, framing, timing, and cuts exactly as they are.
```

## Blender-source guardrails

- **Preview artifacts are not content.** Viewport grid, gizmos, selection
  outlines, axes, and empty-world backgrounds are artifacts of the preview —
  name what replaces them; never preserve them unless the user asks for a
  deliberately tool-like final look.
- **Blender authored the motion.** No new camera moves, no speed ramps, no
  re-staging, no re-timing. The camera path and cuts are locks.
- **Geometry stays.** Do not add, remove, or reshape objects unless the user
  explicitly requests it; carry the quantity guard on any preserved subject.
- **Audio follows the source.** Blender previews are usually silent — write
  `No audio at all`; if the source has audio the user wants kept, say so and
  keep it. Never invent a score.
- **People get protection.** Real human skin with pores and catchlights — never
  waxy, smoothed, or warped; subjects stay grounded with no cut-out edge or
  halo.
- **One source reference.** Bind `@Video 1` only; add `@Image N` solely as a
  target-appearance reference with its role stated, never as a second master.
- **One look per clip.** Do not combine competing grades, lighting presets, or
  animation-medium styles in a single edit.

## Compose with

- `seedance-prompt-25` — the 2.5 structured-edit grammar and full six-part
  formula; this skill supplies the Blender-source edit block only.
- `seedance-vfx-prompt` — general edit taxonomy and the legacy 2.0 path when
  the user explicitly needs 4K or Fast/Mini.
- `prompt-review` — the gate for every prompt before handoff; missing reviewer
  output is incomplete.

## Self-check

1. `@Video 1` is the sole editing master; any `@Image N` defines target appearance only.
2. The prompt names the Blender source type and states that geometry, motion, camera, timing, and cuts are preserved.
3. The edit changes appearance only — no re-blocking, re-timing, or new camera moves.
4. Preview artifacts (grid, gizmos, overlays) are replaced or removed, not preserved.
5. Object count and placement are locked; the quantity guard is present when a subject is preserved.
6. Lighting is stated physically and matches the new environment.
7. Audio follows the source (silent stays silent); no invented score.
8. People carry grounding and face-protection locks.
9. Source is within the duration ceiling; longer sources are trimmed or split on shot boundaries by the user.
10. The prompt contains the edit block, not a production workflow or generation instructions.
11. `prompt-review` has passed before handoff.
