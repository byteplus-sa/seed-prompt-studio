# AGENTS.md — seed-prompt-studio

Prompt-only workspace for composing Lumina-paste-ready prompts for BytePlus
Seed-family models (Seedance, Seedream, Seed Audio). It writes and reviews
prompts; it never generates, uploads, or persists media.

## Scope and authority

This workspace produces copy-paste prompt blocks. The user pastes them into
Lumina or another Seed-model UI, where generation happens. There is no
generation transport of any kind here — no model API access, no agent tool
servers, no command-line generation. Deterministic rendering, 3D, and assembly
capabilities are out of scope in this standalone workspace. Local
`ffmpeg`/`ffprobe` are permitted for read-only analysis frame extraction only.

Deliver prompts in chat by default. Save a draft under
`projects/<name>/prompts/` only when the user explicitly asks.

Task scope does not expand into unrelated global configuration, external
publishing, or production edits. Keep project-specific instructions in
`.agents/`. Do not create a second competing `.agents/AGENTS.md`.

Before editing, inspect relevant files, plans and existing Git changes. Never
revert, overwrite or clean up work you did not create for this task. Ask for
missing information when it materially affects the intended result.

## Operational contracts

Load only the contract relevant to the current request:

| Need | Tracked source |
| --- | --- |
| Prompt review policy and stable rule IDs | [rules.json](.agents/contracts/rules.json) and [production policy](.agents/contracts/production-policy.md) |
| Requested camera, lens, lighting, grade, acting, pacing, blocking, medium axes | [Directorial axes](.agents/contracts/seedance-reference.md) |
| Canon, props, screens and reference roles | [Element identification](.agents/contracts/element-identification.md) |
| Dialogue synchronization and assembly | [Audio-video alignment](.agents/contracts/audio-video-alignment.md) |

## Routing

Compose only the axes the user requested. One grade, one dominant lighting
direction, and one or two camera moves per clip unless the explicitly
requested choreography needs more.

| Intent | Route |
| --- | --- |
| Brief shaping | `brief-intake` |
| Reference-video reverse engineering (breakdown → element, storyboard, Seedance prompts) | `template-factory` |
| Seedance 2.5 prompt grammar | `seedance-prompt-25` |
| Seedance 2.0 / 4K legacy grammar | `seedance-prompt-20` |
| Filipino/Tagalog dialogue direction | `seedance-prompt-25-filipino` |
| Acting direction and emotional intensity | `seedance-acting-console` |
| Camera / lens / lighting / pacing presets | `seedance-camera-presets`, `seedance-lens-presets`, `seedance-lighting-presets`, `seedance-pacing-presets` |
| Animation styles / Blender-video edit prompts | `seedance-animation-styles`, `seedance-graybox-world` |
| Motion design / music video / restoration / VFX edit prompts | `seedance-motion-design`, `seedance-music-video`, `seedance-restoration`, `seedance-vfx-prompt` |
| Seedream image prompts, character sheets, location plates | `seedream-prompt`, `seedream-character-sheet`, `seedream-location-asset` |
| Seed Audio prompts | `seed-audio-prompt` |
| Audio commercial prompts (story arc, cast, tagline) | `seed-audio-commercial` |
| UGC ad modes / UGC motion presets | `ugc-ad-modes`, `ugc-motion-presets` |
| Color grade sentence | `color-grade-palettes` |
| Scene structure / staging references | `tig-scene-engine`, `tig-blocking-map` |
| Prompt QA | `prompt-review` |

## Skills and orchestration

One skill provides one capability. Leaf skills remain independently usable;
composition hints are prose, not directives to load siblings. Load only the
specialists needed for the current request. `template-factory` is this
workspace's declared orchestrator for reference-video reverse engineering; it
sequences the prompt leaves and the `prompt-review` gate.

This workspace ships prompt-composition skills only. Generation pipelines,
deterministic-graphics renderers, 3D/animation tooling, and media-processing
skills are not installed here; do not attempt their workflows. Local
`ffmpeg`/`ffprobe` are analysis-only — never generation, assembly, or
transcoding.

Keep skill metadata concise and valid YAML. Move substantial conditional
modes and examples into focused same-skill references.

## Prompt invariants

- Run `prompt-review` for every generation-bound prompt before handoff.
  Resolve CRITICAL/MAJOR findings and re-review changed prompts. Missing
  reviewer output is incomplete.
- Freeze the exact prompt text before handoff; changed inputs invalidate
  review.
- Source video is inspected by an agent video pass when the client can watch
  it, or by local `ffmpeg`/`ffprobe` frame extraction read as images when it
  cannot; never claim to have watched footage you could not access.
- Preserve exact canonical descriptors where applicable. Prefer positive,
  observable direction; necessary edit-scope exclusions are allowed.
- Identify all visible props; only threshold-qualified props need locked
  references. Incidental generic props may be described.
- Do not bake captions, taglines, CTAs, end cards or other overlay text into
  prompts; on-screen text is added in post by the destination workflow.
- Static sheets use visible-design criteria; narrative shots need action and
  intent; audio uses its requested sound arc. Do not apply narrative tactics
  to every static sheet.
- Moderation rejection is evidence to diagnose, not proof of a false
  positive. Revisions remain legitimate and authorized.
- Exact-copy, typography, logo, UI, and deterministic HTML/CSS/SVG work is
  out of scope; say so rather than improvising a prompt.

## Local state and naming

`projects/<project-name>/` holds durable local drafts, never a default Git
staging target.

| Location | Contents |
| --- | --- |
| `projects/<project>/project.md` | Optional brief and confirmed choices |
| `projects/<project>/prompts/prompt_<asset-stem>.md` | Saved prompt drafts, immutable after handoff |
| `projects/<project>/elements/<element-id>/` | Optional element prompt records and acquired-asset provenance |
| `projects/<project>/frames/` | Analysis-only frame-extraction scratch; never a deliverable |

Folders and IDs use lowercase kebab-case. Prompt snapshots are saved only on
explicit request; chat-only is the default.

## Security and change boundaries

No credentials belong in this workspace; prompts and drafts contain no
secrets, signed URLs, or account data. Never output or commit credentials.

Never commit or push repository changes unless requested. When authorized,
stage only task-owned files. Clean up only your own temporary artifacts;
never revert or discard work you did not create.

## Verification

`prompt-review` is the QA gate for every prompt. For repository changes run
`git diff --check`; this workspace ships no code, so unit, lint, type, and
build checks are not applicable.

Shared skills are maintained in an upstream source checkout and pulled in with
`/sync-skills` (see README Maintenance). The command never overwrites the local
`template-factory` fork and leaves its changes uncommitted for review.
