---
name: template-factory
description: >-
  Reverse-engineer a reference video into a Lumina-ready prompt package: a
  structured video breakdown, Seedream element prompts (characters, locations,
  props), storyboard prompts, and Seedance 2.5 video prompts, each passed
  through prompt-review. Prompt-only — never generates media, uploads files,
  or submits tasks. Use when the user provides a video or keyframes to
  replicate its style, composition, and grammar; adapt it to new inputs; or
  build a reusable template recipe.
---

# Template Factory

Turn a reference video ("pin") into a prompt package through a reusable
"template" (the recipe). One run produces: a validated `VideoBreakdown`
analysis, Seedream element prompts, storyboard prompts, and Seedance 2.5
prompts — delivered as copy-paste blocks for Lumina.

This skill is a **declared orchestrator**: it sequences the prompt-composition
leaf skills and the `prompt-review` gate. It never generates media, never
uploads files, and never submits a task.

## What one run produces

| Output | Authored with | Notes |
| --- | --- | --- |
| Video breakdown | [analysis-prompt.md](references/analysis-prompt.md) + [breakdown-schema.json](references/breakdown-schema.json) | Shots, elements, style, camera, audio; user-approved before prompts |
| Motion review | [motion-review-prompt.md](references/motion-review-prompt.md) | Second pass; merged by `shot_index`; the anti-static fix |
| Seedream element prompts | `seedream-character-sheet`, `seedream-location-asset`, `seedream-prompt` | Characters, locations, props above the threshold |
| Storyboard prompts | [storyboard-prompts.md](references/storyboard-prompts.md) | One panel per shot by default; sketch grid default |
| Seedance 2.5 prompts | `seedance-prompt-25` + [slot-mapping.md](references/slot-mapping.md) | Per-shot natural duration; ordered `@Image N` bindings |
| Reusable recipe (optional) | [template-schema.json](references/template-schema.json) | Locked grammar + replaceable inputs + adaptation rules |

## Input handling (no media tools in this workspace)

This workspace has no video-understanding, upload, or frame-extraction tools.
Resolve one of these modes before analysis and record which one was used:

| Mode | When | What you do |
| --- | --- | --- |
| **Agent video pass** | Your client can watch the provided video | Analyze directly with the analysis prompt |
| **Keyframe set** | You cannot watch the video | Ask the user for a keyframe set — at least one frame per shot, mid-shot preferred — plus total duration, aspect ratio, and any audio notes. Read the frames as images. |
| **External pass** | The user can run a video-capable tool | Hand the user the analysis prompt to paste there; they return the JSON |

**Never claim to have watched a video you could not access.** When working
from keyframes, mark timing, audio, and motion observations as estimates and
record `source: keyframes` in the analysis metadata.

## Core operating model

- Act as the single manager communicating with the user.
- Treat `projects/<project>/templates/<template-id>/` as production memory
  when the user requests saved drafts.
- Never infer approval. A completed draft is `review`; only the user approves
  the breakdown, the element set, and the final prompts.
- **Every prompt this factory hands off — element sheet, storyboard, or
  Seedance — must pass the `prompt-review` gate first.** Missing reviewer
  output is incomplete.
- Replicate style, composition, and grammar. Do not clone copyrighted
  footage. De-identify real people in analysis.
- Deliver prompts in chat by default; save drafts only on explicit request.

## Pipeline (one stage at a time)

```text
pin_provided → breakdown_draft → breakdown_approved → motion_reviewed
  → element_prompts → storyboard_prompts → seedance_prompts → package_handoff
```

### 1. Intake

Confirm the input mode, the pin's duration and aspect ratio, the intended
reuse (replicate as-is, adapt to new subjects, or build a reusable recipe),
and the brand/identity mode from the analysis prompt's table. Record open
questions rather than guessing.

### 2. Analysis

Run the analysis prompt (agent pass, keyframes, or external). Validate the
returned JSON against `breakdown-schema.json` by inspection: valid JSON, all
required fields, ordered non-overlapping shots, every `keyframe_index` and
`in_shots` entry referring to an existing shot, element ids kebab-case.

Write `breakdown.md` (readable) and keep `analysis.json` beside it when the
user requests saved drafts. **Gate A: the user reviews the breakdown** before
any prompt authoring. Revise and re-present until approved.

### 3. Motion review

A static action description is not enough to reproduce how a template moves.
Run the motion-review pass on the same source. Merge results into the
analysis **by `shot_index`, never by array position**, as
`shots[].motion`. Motion evidence may enrich the approved breakdown without
changing timing or action; if it changes an approved decision, mark the
affected prompts stale and re-present.

### 4. Element prompts

Identify required canonical inputs from the approved breakdown. Apply the
workspace prop threshold: branded, recurring, story-critical, or
scene-variant wearables need a separate locked reference; incidental objects
may be described in text inside the Seedance prompt.

| Element type | Prompt skill | Output reference id |
| --- | --- | --- |
| character | `seedream-character-sheet` | `char_<id>` |
| location | `seedream-location-asset` | `loc_<id>` |
| prop | `seedream-prompt` | `prop_<id>` |
| invented or illustrative screen/card imagery | `seedream-prompt` | `screen_<id>` |

Bind the breakdown's flagged keyframe as `@Image 1` (image-to-image) where
one exists. Exact typography, logos, UI, title cards, posters, product
layouts, and price/CTA treatments are out of scope in this workspace — say so
and hand that element back to the ark-director workspace instead of
improvising a prompt.

Present the element prompt set; the user approves it before storyboard and
Seedance prompts depend on it.

### 5. Storyboard prompts

Author storyboard prompts per [storyboard-prompts.md](references/storyboard-prompts.md):
one panel per shot by default, monochrome sketch default, single-image grid
default, with an explicit panel budget winning over the shot count. The
storyboard is a prompt deliverable — the user pastes it into Lumina.

### 6. Seedance prompts

Compose the Seedance 2.5 six-part prompt per [slot-mapping.md](references/slot-mapping.md)
via `seedance-prompt-25`:

- Subject and Scene from element descriptors, copied word for word and bound
  to `@Image N`.
- Action from `shots[].action` plus the motion-review imperative wording.
- Visual style ordered lighting → lens → grade → film look.
- Camera from the breakdown and per-shot camera fields; ≤2 moves per clip.
- Audio in bracket syntax; dialogue verbatim in `{braces}`; `No audio at all`
  when the source is silent.

Prefer one clip per shot at natural duration. When the requested output
exceeds one supported clip, split on shot boundaries rather than compressing
the template. Include the parameter block (duration, resolution, ratio)
beside the prompt, and note that the user may trim parameters the Lumina UI
does not expose.

### 7. Package handoff

Deliver each prompt as its own fenced copy-paste block, labeled with its
element or shot id, followed by the ordered reference list and the parameter
block. Run `prompt-review` on every block before handoff and report the
review outcome. Optionally produce the reusable recipe against
`template-schema.json`: `locked_grammar`, `replaceable_inputs`,
`adaptation_rules`. Never bundle source media, identities, brands, or signed
URLs into the recipe.

## Route specialist work

| Need | Skill |
| --- | --- |
| Brief shaping before analysis | `brief-intake` |
| Video analysis, motion review | This skill's references (agent / keyframes / external pass) |
| Character sheets | `seedream-character-sheet` |
| Location plates | `seedream-location-asset` |
| Props and other invented imagery | `seedream-prompt` |
| Storyboard prompts | [storyboard-prompts.md](references/storyboard-prompts.md) |
| Seedance prompt grammar | `seedance-prompt-25` |
| Filipino / Tagalog dialogue | `seedance-prompt-25-filipino` |
| Acting, camera, lens, lighting, pacing axes | `seedance-acting-console`, `seedance-{camera,lens,lighting,pacing}-presets` |
| Grade sentence | `color-grade-palettes` |
| Prompt quality gate | `prompt-review` |
| Exact typography, UI, logo, poster, product layout | Out of scope — hand back to ark-director |

## Revisions

Write every revision as: locked decisions, requested delta, acceptance
criteria, known rejections, invalidation scope. Change one of prompt wording,
reference bundle, or motion design at a time.

## File layout (saved drafts, on request only)

```text
projects/<project>/templates/<template-id>/
├── analysis.json               # validated VideoBreakdown
├── breakdown.md                # readable rendering
├── motion-review.md            # merged motion evidence
├── recipe.json                 # optional reusable template
└── prompts/
    ├── prompt_<element-id>.md  # Seedream element prompts
    ├── prompt_storyboard_v01.md
    └── prompt_<shot-id>.md     # Seedance prompts
```
