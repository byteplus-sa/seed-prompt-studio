# Storyboard prompts

Author storyboard prompts for the template's panel plan. Prompt-only: the
deliverable is the prompt (and its ordered reference list), which the user
pastes into Lumina.

## Panel plan

- One panel per identified shot by default. Honor an explicit user panel
  budget and keep a documented mapping from every shot to a panel or a
  combined beat.
- Render style defaults to **rough pencil sketch, monochrome graphite, no
  color**. Honor an explicit limited-palette, full-color, or
  standalone-production-panel request and record the decision.
- Delivery defaults to a **single-image grid** in reading order. Use separate
  panels when the user wants individually usable images.

## Per-panel prompt (separate-image mode)

Use only the sections that add value. Keep the prompt within the live tool
limit (Seedream enforces 4,000 characters).

```text
References:
@Image 1: character identity — [character]
@Image 2: location geometry — [location]
@Image 3: prop identity — [prop]
@Image 4: composition control — [layout only]
@Image 5: approved style — [style, palette, and lighting only]

Task:
Image-to-Image (I2I) — single cinematic storyboard panel

Panel purpose:
[The new story information or emotion this panel communicates.]

Subject and decisive moment:
[One frozen moment. Name every visible subject, pose, expression, action, and
prop. Bind identity and prop references inline.]

Setting and state:
[Location, time, weather, persistent landmarks, visible object state. Bind the
location reference inline.]

Staging and continuity:
[Screen-left/right positions, foreground/midground/background, eyelines,
distances, overlaps, travel direction, entrances/exits, and what must match the
previous panel.]

Style:
[Default: "rough pencil sketch, monochrome graphite, no color" unless the user
requested full color or a specific sketch medium. Bind the style reference
inline when provided.]

Lighting:
[Source, direction, quality, color, and atmosphere. In sketch mode, describe
lighting as directional shading cues (e.g. "light from upper left, cast
shadows to the lower right") rather than color temperature and material
response. Never describe a light source as a point or dot: name the physical
emitter and state its full-frame effect.]

Composition:
[Aspect ratio, shot size, camera height/angle, lens intent, framing, depth, and
focus priority.]

Constraints:
Quality: [appropriate draft or final quality]
Preserve: [approved identity, geometry, pose, state, and style]
Exclude: [only material faults; no text overlays, labels, storyboard borders,
watermarks, unintended subjects, duplicated faces, or control-guide marks]
```

For text-to-image, omit `References` only when no canonical element sheets
exist. When character, location, or prop sheets exist, use image-to-image and
bind them with exact `@Image N` tokens. Reference inventory alone is
insufficient: repeat each binding in the section it controls.

## Multi-panel prompt — single-image grid (default)

```text
Task:
Single-Image Grid Storyboard — [N] panels in one image

Grid contract:
Generate ONE single image containing [N] storyboard panels arranged in a
[rows]×[cols] grid, reading left-to-right, top-to-bottom. Each panel is a
separate frozen decisive moment in narrative order. Separate panels with thin
divider lines. Place a small panel number in the top-left corner of each cell.
Keep recurring character identity, wardrobe, location geometry, prop design,
palette, and rendering style consistent across all panels.

References:
@Image 1: character identity — selected character sheet
@Image 2: location geometry — selected location sheet
@Image 3: prop identity — selected prop sheet

Global visual canon:
[Stable identity, location, prop, style, aspect ratio, and lighting rules.
Default render style: "rough pencil sketch, monochrome graphite, no color"
unless full color or a specific sketch medium is requested. Bind element
references for identity, geometry, and prop form even in sketch mode.]

Panel 1 — [panel ID and purpose]:
[Decisive moment, staging, camera, and state.]

Panel 2 — [panel ID and purpose]:
[Decisive moment, staging, camera, and state change.]

[Continue in order.]

Constraints:
Return one single image with [N] panels in a [rows]×[cols] grid. Thin dividers
between panels. Small panel numbers in top-left corners. No speech bubbles, no
captions outside panel numbers, no watermarks, no decorative borders. Preserve
character count, identity, handedness, screen direction, location landmarks,
and prop state unless a numbered panel explicitly changes them.
```

Prefer `2048x2048` or wider for a 3×3 grid so each panel stays legible. A
panel that must become a video keyframe should be re-authored as a standalone
image prompt using the same canon and its recorded panel section.

## Multi-panel prompt — separate images

```text
Task:
Sequential Generation — [N] separate cinematic storyboard images

Sequence contract:
Generate a cohesive ordered set of [N] separate images, one image per numbered
panel. Keep recurring character identity, wardrobe, location geometry, prop
design, palette, and rendering style consistent. Each image must depict one
frozen decisive moment, not a collage or multi-panel grid.

References:
@Image 1: character identity — selected character sheet
@Image 2: location geometry — selected location sheet
@Image 3: prop identity — selected prop sheet

Global visual canon:
[Same canon block as the grid prompt.]

Panel 1 — [panel ID and purpose]:
[Decisive moment, staging, camera, and state.]

[Continue in order.]

Constraints:
Return [N] separate images in narrative order. No captions, borders, panel
numbers, speech bubbles, watermarks, or contact-sheet layout inside the images.
Preserve character count, identity, handedness, screen direction, location
landmarks, and prop state unless a numbered panel explicitly changes them.
```

## Prompt length budget

The Seedream prompt limit is **4,000 characters**. Count before handoff; above
~3,800, condense:

1. Shorten panel descriptions to one or two tight sentences each; keep the
   detailed continuity ledger in `breakdown.md`, not in the prompt.
2. Compress reference descriptions to one-line summaries:
   `@Image 1: Mara — athletic build, crimson costume, gold accents`.
3. Merge global canon and constraints into one compact block.
4. Split by scene or act into two grid prompts when condensing is not enough.

Condensed template for large panel counts:

```text
Single-Image Grid Storyboard — [N] panels in a [rows]×[cols] grid, left-to-right, top-to-bottom. Thin dividers, small panel numbers 1-[N] in top-left corners. [sketch or color style].

@Image 1: [character — one-line identity + costume summary].
@Image 2: [character/location — one-line summary].
@Image 3: [location/prop — one-line summary].

Use face/body/costume from @Image 1 for [name] and @Image 2 for [name], rendered as [sketch or color]. Preserve [location] geometry from @Image 3.

Panel 1: [one-two sentence decisive moment, staging, camera].
Panel 2: [one-two sentence decisive moment, staging, camera].
[Continue for all panels.]

No speech bubbles, no captions, no watermarks. Preserve identity, wardrobe, screen direction, [key prop] across all panels.
```

## Review gate

Every storyboard prompt is generation-bound: run `prompt-review` on the exact
prompt and its ordered reference list before handoff. Check element bindings
(`@Image N` indices match the reference list), positive-only directing,
sketch/color consistency, continuity of props and screen direction, and the
4,000-character budget.
