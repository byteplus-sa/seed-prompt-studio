# Element identification

Identify visible elements during draft breakdown; approve required references before dependent production generation. Static asset prompts create canon and therefore do not require that same asset to be approved already.

| Category | Treatment |
| --- | --- |
| Characters | Canonical sheet for recurring or identity-critical on-camera characters; approved descriptors for incidental people |
| Screen-only people | Lock the visible call UI; describe moving callers and dialogue in text rather than attaching their character sheets as static screen content |
| Locations | Canonical location for recurring or geography-critical spaces; scene-level direction/keyframes for incidental settings |
| Held or operated props | Identify every object, then apply the threshold below; holding an object alone does not require generating a sheet |
| Screens and text | Use deterministic HTML-entrypoint graphics for exact copy/layout; use Seedream only for synthesized screen imagery, then finish exact UI/text deterministically |
| Brand/title cards | Deterministic card for exact typography, logo, product, price, or CTA geometry; preserve source logo rights and visual design |
| Audio | Separate reusable music/SFX/ambience when the requested workflow needs them; native video audio does not require a redundant audio generation |
| Wearables | Always-worn outfit items belong in character design; scene-variant wearables are separate assets when consistency is needed |

## Prop threshold

A branded, recurring (two or more shots), or story-critical object needs a
**locked reference**. Generic one-off cups, pens, food, or background furniture
can be directed in text. A scene-level keyframe is sufficient for a one-off
composition that needs visual review. Do not invent a new generation requirement
merely because an object is visible.

A locked reference is an approved local asset with a content hash — not
automatically a Seedream generation. Prefer acquisition over generation for
real brands and products (see below).

Held/operated canonical props remain separate from the character sheet. Reference only assets actually used by the shot; preserve exact canonical identity descriptors where applicable. Record unresolved required assets and defer their dependent generation.

## Brand, logo, and product acquisition

When the brief authorizes a real brand, logo, packshot, or labeled product:

1. **Reuse** an existing project asset if its content hash still matches the
   needed identity.
2. **Acquire** an official or authorized web/pack shot/logo when no usable local
   file exists — download into `projects/<project>/elements/<element-id>/`, keep source copies under
   `refs/` when useful, and record provenance (source URL, download time,
   SHA-256) in the element manifest and/or `PROVENANCE.md`.
3. **Promote** the acquired file to the canonical asset name
   (`prop_…`, `screen_…`, `card_…`), set `source: web_download` or
   `user_supplied`, `generation: none`, and obtain explicit
   `selected_variant` / `approved` from the user.
4. **Generate with Seedream only** when no usable real asset exists, the user
   requests a stylized or fictional substitute, or acquisition is blocked.

Do not invent a fake packshot or logo with Seedream when a downloadable official
or authorized reference is available. Trademark and rights remain with the brand
owner; keep acquired assets local to the production unless publishing is
explicitly authorized. Unknown or unauthorized brands stay de-identified in
analysis and may use placeholder descriptors until the user supplies or
authorizes real identity.

Acquired brand/product assets skip `prompt-review` because there is no
generation-bound prompt. They still require explicit user selection or
approval before dependent prompts depend on them.

## Exact-graphics boundary

Exact copy, typography, logo placement, UI, price/CTA treatment, safe areas,
product order, and repeatable poster geometry are **out of scope in this
workspace**: deterministic HTML-entrypoint graphics are not a capability here.
Treat such elements as out of scope instead of improvising a prompt, and do not
ask the model to render them.

Use Seedream for synthesized photography, characters, locations, illustration,
materials, or expressive textures. If a deliverable needs a generated base
beneath exact copy and graphic geometry, the selected text-free image is the
prompt deliverable and deterministic finishing happens outside this workspace;
the generated layer keeps its prompt and review evidence here. If both a
white-background model reference and transparent delivery cutout are needed,
author and label them as separate prompts. Never globally remove white from a
product image when that would erase labels, highlights, or internal white
details.

## Reference footage and brand-ad inspiration

When the brief cites a real brand video ad or other footage for visual or motion
inspiration (as distinct from locking a logo/packshot still):

1. **Obtain a watchable copy.** Ask the user for a local file, or a direct link
   the user can open. This workspace does not upload media.
2. **Analyze in one of two modes.** Run an **agent video pass** when the client
   can watch the video; otherwise extract frames locally (`ffmpeg`/`ffprobe`)
   and read them as images — the normal case. Never claim to have watched
   footage you could not access; sampled motion and audio stay inferences.
3. **Do not treat transcripts, scripts, or marketing write-ups as the
   reference.** They may supplement dialogue or claims after the video is
   inspected, but they do not establish shot grammar, motion, or composition.
4. **Route** full reverse-engineering (breakdown → recipe → optional remake)
   through `template-factory`; a lighter “what is this ad doing visually?” pass
   uses the same two analysis modes.

Inspiration footage is analysis/reference media, not automatic canon. Brand
still acquisition above still governs logos and packshots used as locked
elements.

## Reference eligibility

Elements define persistent identity/design. Approved derivative panels define composition and continuity, with recorded source hashes. Changed source hashes invalidate a panel's previous eligibility. Analysis sketches, blocking maps, and rough control boards remain analysis-only by default; translate their movement into text. An explicitly requested supported grid-conditioning mode needs a separately selected composition reference and documented role, never inferred approval of a control image.
