---
name: seedance-music-video
description: >-
  Write Seedance 2.5 music-video prompts from a song, artist brief, or requested
  format. Map song sections, beat density, performer intent, camera, lyric timing,
  the black-sync video timing reference, native versus supplied audio, and natural
  scene duration into the six-part formula. Cover rap, dance, performance, narrative, abstract, vertical, and custom
  formats. Use for song-driven visual direction, lyric/performance videos, or
  music-video revisions. This prompt-only leaf does not generate media; the caller
  composes only requested specialist axes and owns review and submission.
---

# Seedance Music Video

Write ready-to-use Seedance 2.5 prompts for music videos. The music is the
fixed brief: every visual decision is planned backward from the track's
sections, beats, and energy. Make the format, the song map, the beat contract,
and the genre lock visible in the prompt, not just the scene content.


## Input and output contract

Input: song structure, desired format, performer locks, beat contract, audio intent, and the timing-reference mode.

Output: a music-video direction block or complete video prompt.

## Procedure and reference loading

Use output-templates for the requested deliverable, specialized-formats for rap/vertical/custom format details, and the core workflow below for song/beat/audio decisions.

Read only the mode-specific resources needed for the request. Reference paths
mentioned in prose are relative to this skill directory unless a link says otherwise.

- [Output Templates](references/output-templates.md) — Output formats.
- [Specialized Formats](references/specialized-formats.md) — Format-specific rules; Custom-format procedure.
- [Hypothetical repairs](references/music-repairs.md) — Read when pacing, lyric timing or a revision fails the intended musical relationship.

## Submission boundary and failure behavior

The caller owns production authorization, the exact request preflight, and the
complete hash-bound prompt review. A leaf returns its prompt package without
loading sibling skills. An explicitly declared orchestrator may coordinate the
review and submission stages. Missing required inputs remain unresolved; a draft
or technical success does not establish user approval. Preserve optional timing,
the three-image sampling default where applicable, and the requested delta.

## Source basis

- Official Seedance 2.5 prompt guide, launch blog, and ModelArk docs — audio
  bracket syntax, `@Audio N` timing-authority contract, the one-take singer
  example, timestamp grammar, and limitations. See `seedance-prompt-25`.
- Music-video craft and beat-sync practice — format taxonomy, song-section
  energy mapping, and section-relative cut density (sources below).
- Genre conventions — per-genre visual recipes, vertical-vs-landscape norms.
- **Field-tested learnings from `mv-bryce-vine` (2026-08-20):** the timestamped
  lyric timeline technique (section 3b), native audio re-performance behavior
  (section 3a), the black-sync timing-reference carrier (section 3, take t04),
  and ASR-based lyric
  verification (section 3c) were derived from three generation takes where
  large `{...}` blocks caused lyric dropouts.
- **Field-tested black-sync follow-up (t04 vs t03 baseline):** carrying the
  master inside a pure-black video container as `@Video 1` (section 3)
  preserved the source waveform through encoding and produced strong
  transient alignment in the co-generated native audio; adopted as the
  timing-reference carrier, replacing the standalone `@Audio 1` file
  entirely. Historical local evidence, not a capability guarantee.

Key third-party sources (accessed 2026-08-20):
[AI music-video production workflows](https://www.creativeainews.com/articles/how-to-make-ai-music-video-2026/),
[Beat-synced 30s edits](https://aitoolsguidebook.com/en/articles/ai-music-video-tutorial/),
and [music-video section-arc planning](https://blog.celtx.com/how-to-write-a-music-video-script/).
The recipes generalize these principles rather than copying published prompts.

## Format & genre bank

Read `references/music-video-recipes.md` after identifying the user's format
and genre. Use one genre recipe plus the matching format rule; do not paste the
whole bank into a prompt.

Formats: `performance`, `narrative`, `conceptual`, `lyric`, `visualizer`, `hybrid`.
(Here and throughout, `visualizer` means a music / audio-reactive visualizer,
not a data or code visualizer.)
Genres: `hip-hop-trap`, `edm`, `pop`, `indie-dream-pop`, `rock`, `rnb-soul`,
`country`, `classical-acoustic`, `abstract-visualizer`, plus a custom template.

## Core principle

The song is the brief. The music drives the video, not the other way around.
Do not write a story first and then smear a song over it — map each song
section to a visual assignment, and let the track's energy, tempo, and vocal
set the pacing, framing, and cut density.

For every prompt, define:

```text
format                 which video type this is (and its direct/indirect address)
song map               each section → visual assignment, energy, and end state
performance ratio      how much screen time is performer vs atmosphere per section
beat contract          which audio events the cuts and camera land on
audio treatment        native audio, or a master carried as the black-sync @Video 1 timing reference
genre lock             one recipe controlling palette, lighting, camera, and rhythm
exclusions             only contradictions that would break the format or genre
```

## Prompting workflow

### 1. Resolve the format

Map the user's request to one format. The format decides the address mode,
which shots matter, and where the performer sits in the frame:

- **Performance** — the artist lip-syncs to camera (direct address); energy and
  mouth-to-vocal sync carry it. Close-ups, medium shots, full-body dance.
- **Narrative** — a short film; the song is the score (indirect address);
  characters never address the camera. Lyric relation can be literal
  (illustration), emotional (amplification), or deliberately detached
  (disjuncture).
- **Conceptual** — no plot; one governing visual rule, image, or metaphor that
  encodes the track, executed with mechanical rigor.
- **Lyric** — the lyrics are the primary visual; word-level sync to the vocal.
  The model renders provisional captions; exact text is set in post.
- **Visualizer** — abstract, audio-reactive imagery with no performer and no
  lyrics; the visuals "image the sound" (waveforms, particles, geometry).
- **Hybrid** — combines performance with narrative or concept. One possible
  arrangement places performance in choruses and narrative in verses; choose
  their allocation from the actual song and intended audience effect.

When the format is unspecified, recommend one from the intended audience effect
and supplied track evidence, with a brief reason. Mark the recommendation
provisional when these inputs are missing. Narrative intimacy, a held conceptual
image, performance energy and a hybrid are alternatives, not genre assignments;
choose only what serves this brief.

### 2. Map the song to a visual plan

Start with the available evidence: listen to the supplied track when accessible,
using a supported media tool; otherwise use the user's section map and label it
as supplied. If neither exists, provide a **provisional treatment**, not invented
BPM, timestamps, lyrics or claims of having heard the song. Ask for audio only
when exact synchronization depends on it; continue untimed creative work.

Record audible changes (density, vocal delivery, texture, silence, accents and
phrase boundaries) separately from proposed visual choices. A genre or section
name does not establish its energy. A chorus may become quieter; a through-composed
track may have no chorus at all.

| Relationship | When it serves the brief | Visual choice |
| --- | --- | --- |
| Escalation | Audible build or requested release | Increase scale, movement or cut density on the supported change |
| Restraint | Intimacy, quiet refrain or sustained tension | Hold framing; let a small performance change carry the section |
| Repetition | Ritual, obsession or a deliberately stable hook | Repeat the composition or action with intentional continuity |
| Counterpoint | Requested emotional tension between image and sound | Hold calm imagery against dense sound, with a clear intended effect |
| Departure | Actual texture change or requested structural contrast | Change one visual rule without inventing a bridge |

Give each section one primary assignment and an end state. Choose its relationship
from the audible evidence and the audience effect; keep repeated hooks unchanged
when repetition is the point. Recipe energy arcs are **creative heuristics**, not
API rules or requirements for every song. Preserve a requested continuous take.

### 3. Set the audio contract

Choose one of two audio modes; state it explicitly.

**Native audio (default when no master is involved).** Seedance co-generates
audio and video in one pass. Use the bracket syntax in the Audio slot and
inside `{...}` for sung or spoken lines. Choose exactly one of these patterns —
they are mutually exclusive, do not combine them:

```
(Soft, rhythmic piano music plays in the background)
{the exact sung line}
<the kick hits on each downbeat>
```

```
No background music. Keep only the singer's voice and the room ambience.
```

```
No audio at all.
```

When the clip must sit under an existing master in post, add a direct control
line so the model does not invent music (the generated file may carry no usable
track — re-mux the master in assembly).

**Black-sync video reference (required when the performance follows a supplied
or Seed Audio master).** Do not send the song as a standalone `@Audio 1` file.
Embed the track in a **pure-black video container** and send it as `@Video 1` —
the timing and soundtrack authority. Field evidence (`mv-bryce-vine` t04 vs
t03) shows the container preserves the source waveform through encoding and
yields strong transient alignment in the co-generated native audio.

Container preparation is a **user-side step** — this workspace does not
assemble media. Give the user the recipe:

| Property | Value |
| --- | --- |
| Video | pure black frames, H.264 |
| Resolution / fps | 1920x1080 at 24fps (or match the target output) |
| Audio | the master segment, muxed as AAC (320 kbps) |
| Duration | the trimmed track segment (≤30s), ending cleanly on the last word |
| Container | MP4; audio starts at frame 0 |

```bash
ffmpeg -f lavfi -i color=c=black:s=1920x1080:r=24 -i master.mp3 \
  -shortest -c:v libx264 -pix_fmt yuv420p -c:a aac -b:a 320k black-sync.mp4
```

Bind the container as the timing authority and enable native co-generation
(`generate_audio: true`):

```
[Timing & Soundtrack Authority]
@Video 1 provides the exact soundtrack, beat timing, vocal rhythm, and all
temporal pacing for the entire video. The music, vocals, and pauses in
@Video 1 define when every visual beat, cut, gesture, and lip movement occurs.
Match <performer>'s visible mouth only to the vocals in @Video 1.
```

The exact sung lines must appear in the Seed Audio prompt (when one exists) and
in the Seedance prompt inside `{...}` — no paraphrasing, no reordering. If one
changes, both change. The generated soundtrack is a re-performance of the
reference, not a copy (section 3a); verify lyric coverage (section 3c), and
when exact soundtrack fidelity is required, have the user re-mux the original
master onto the approved visuals in the destination workflow. The timestamped
lyric timeline (section 3b) stays mandatory for dense vocals.

**No bare-audio route.** In master-locked mode the song always reaches Seedance
inside the black-sync container — never as a standalone `@Audio 1` file. This
ban is scoped to song masters: dialogue-track lip-sync bindings taught by
`seedance-prompt-25` and `seedance-prompt-25-filipino` are unaffected. If the
user cannot run the mux, hand them the recipe above first (any basic editor
works); write the prompt only once the container exists. The timestamped lyric
timeline (section 3b) and verification (section 3c) apply unchanged.

### 3a. Native audio caveat: re-performance, not reproduction

When `generate_audio` is enabled with the black-sync timing reference, the model
**re-performs** the track — it does not copy the reference bit-for-bit.
Treat the generated soundtrack as a candidate to inspect, not an exact copy.
Retain the supplied master for deterministic assembly when exact fidelity matters.
These observations are historical local evidence, not a capability guarantee.

### 3b. Timestamped lyric timeline (for performance videos with lip-sync)

When the performer must lip-sync the full verse and complete lyric coverage
is critical, **do NOT** group all lyrics into one or two large `{...}` blocks.
Instead, bind each lyric line to its own timestamped slot:

```
[2-4 seconds] { I used to pray before I went to bed, }
[5-7 seconds] { back when I was like eleven or ten. }
[7-8 seconds] { I don't remember the facts, }
```

(Example lines quote the rights-holder-authorized `mv-bryce-vine` project
track; the technique itself is lyric-agnostic.)

Add an explicit mandate:

```
The performer performs every line below in full, in order, at its timestamp.
No line may be skipped, shortened, mumbled, or reordered.
```

**When to use this technique:**
- Rap or fast-paced vocal delivery (high dropout risk)
- Any performance video where complete lyric coverage is a hard requirement
- When the audio reference has dense, continuous vocals with no long pauses

**When NOT needed:**
- Ballads with long pauses between lines
- Videos where the visual story matters more than lyric accuracy
- Native audio where the model has space to breathe

**How to get the timestamps (ASR-to-timeline procedure):**

1. Ask the user to run ASR (or supply a transcript) on the audio master; treat returned word timings as user-supplied evidence
2. Extract word-level `start_time_ms` and `end_time_ms` from the result
3. Group words into lyric lines at natural phrase boundaries (commas, periods,
   bar changes)
4. For each line, take the first word's start time and the last word's end
   time
5. Preserve the raw word/line timings as evidence without rounding. Check ASR
   against listening; uncertain words or boundaries remain marked uncertain.
6. If simpler prompt windows help, derive a separate display interval that contains
   the verified phrase (for example floor the start and ceil the end). Do not
   snap offbeat vocals to a grid or replace evidence with rounded timings.
7. Use verified exact intervals or the separately labeled simplified windows as
   `[X-Ys] { line }` slots. Untimed drafts may use ordered lyric cues; do not invent
   measured timings without the audio.

Example ASR-to-timeline conversion:

```
# ASR returns word-level timestamps:
"ever" start=17170ms  "really" start=17530ms  "took" start=17810ms
"charge" start=18170ms  "like" start=18530ms  "a" start=18690ms
"wiring" start=18890ms  "fee" start=19450ms end=19730ms

# Verified phrase interval: 17.170–19.730s; retain as evidence.
# Separate simplified prompt window:
[17-20 seconds] { ever really took charge like a wiring fee, }
```

### 3c. Post-generation lyric and lip-sync verification (master-locked or native audio)

After generation, verify that the output audio contains all expected lyrics:

1. Have the user export the audio track from the generated video
2. Ask the user to run ASR (or supply a transcript) on the exported audio
3. Compare the transcription against the expected lyrics
4. Flag any missing, reordered, or garbled lines
5. If lines are missing: use the timestamped lyric timeline technique
   (section 3b) and regenerate, OR re-mux the original master if exact
   fidelity is needed
6. For lip-sync alignment, have the user mux the original master over the
   generated visuals and check mouth shapes at each timestamp; the
   destination workflow owns the mux

This step is especially important for:
- Rap and fast vocal delivery
- Long verses (10+ lines)
- Native audio generation (re-performance risk)

### 3d. Rap and fast vocal delivery

Rap is the highest-risk vocal mode for lyric dropout:
- Dense, continuous delivery with few pauses
- The model can skip bars without creating obvious silence
- ASR verification (section 3c) is essential after generation

Specific guidance for rap:
- Use per-line timing when complete timed coverage is requested and audio evidence
  exists; otherwise supply ordered lyric cues and mark timing unresolved
- Set the "no line may be skipped, shortened, mumbled, or reordered" mandate
- Run ASR on the output to verify coverage
- Consider splitting very long verses (>15 lines) into multiple clips
- Include a delivery cue: "jaw opening fully on vowels, lips stay in frame
  throughout"

### 4. Set the beat and cut-density contract

The beat grid gives the timing; emotion and story decide what lands on it.
Timestamps are a **time budget, not frame-accurate** — direct the beats in the
prompt, then snap the final cut to the audio master in post.

In-prompt, name each audio event, the visual event it triggers, and their
relationship (simultaneous, leads, or trails):

```
Each direction change lands on a downbeat.
At 5 seconds the camera crash-zooms on the kick.
The chorus cuts land on the beat; the final pose holds on the last hit.
```

For assembly, place markers at the **actual chosen audible events**: a breath,
snare, silence, phrase ending or downbeat. Whole-bar cuts are an optional regular-grid
technique, not suitable for every track. There is no universal early-frame offset:
review synchronization against the master and adjust for the intended perception.
Counterpoint may deliberately hold across an accent; label that relationship.
Irregular meter, rubato and continuous takes do not require grid-aligned cuts.
Use verified timestamps only where precision is requested; event-relative cues
remain valid. Re-check mouth timing for any lip-synced performance.

### 5. Right-size the scenes

Generate per section at its natural duration (4–30s), not per 30-second block.
One song section, one primary event, one visible end state per generation.
Chain approved sections via `return_last_frame` / `first_frame` with a shared
reference bundle and assemble in post. Reserve a 30s single-pass or native
extension for a genuine continuous take (one unbroken performance) where
seamless motion across section boundaries matters more than per-scene iteration.

When the user asks for a lyric video, keep the imagery simple and legible so
the text stays readable; direct word-level pops on the beat via `【word】`, and
keep the type treatment provisional — the exact lyrics and kinetic timing are
set in post.

### 6. Direct the performer

For lip-sync, give the exact line and a delivery cue, and specify the camera
angle to the mouth:

```
The singer looks into the lens and performs in energetic American English,
jaw opening fully on vowels: {I am still the one you know}
```

For acting, use observable cues, not mood words alone — see
`seedance-acting-console`. For a one-take performance, state
`one continuous unbroken shot, no cuts` and list the spaces and events the
camera passes through in order.

### 7. Compose with the six-part formula

Assemble the treatment into the `seedance-prompt-25` formula:

> **Subject + Action or Event + Scene and Environment + Visual Style + Camera Movement/Cut + Audio**

Chain the Visual Style slot as lighting → lens → grade → look. Keep the genre
lock in the Visual Style slot, the beat contract in the Camera/Cut and Audio
slots, and the sung lines in `{...}`. Do not describe the same action twice.

### 8. End with a style seal

Close with one compact sentence that reinforces the genre lock, the palette,
the motion cadence, the beat contract, and the tone. The seal prevents the look
from drifting across the section chain and during later shots.

## Partner-skill routing

This skill owns the music-video layer: format, song map, beat contract, audio
contract, and genre lock. Other axes belong to their owning preset skills — see
the canonical axis→skill table in `.agents/contracts/seedance-reference.md` (including
`seed-audio-prompt` for an original music or vocal master, and `template-factory`
for storyboard prompts; full multi-scene production is out of scope in this
workspace). Never let two skills fight:

**Guardrail:** the genre lock is the sole palette, lighting, and camera source
**unless the user names a specific axis** — then compose that axis with its
owning preset skill and keep exactly one grade, one dominant lighting direction,
and at most two camera moves per clip. Do not stack a second grade or camera
treatment on top of a genre recipe.

## Rights and safety

- Use **original music by default**. Reproducing a real artist's copyrighted
  song in a prompt or as a timing reference requires documented permission from
  the rights holder (the `mv-bryce-vine` project runs under such permission);
  without it, never reproduce a copyrighted song and never write artist-name
  or copycat prompts.
- Voice-cloning a real, named artist's voice requires written consent from the
  artist or estate.
- Keep `watermark: false` where the tool supports it; enable the AIGC watermark only when the
  user explicitly requests it.
- Generate the music master with Seed Audio (or another original source) so the
  release stays distribution-clean.

## Exclusion rules

- Do not use a blanket "no music" — it contradicts the point of a music video;
  use it only inside a specific ambient scene that must stay silent.
- Do not use exclusions as a substitute for positive audio and beat direction.
- Exclude only likely contradictions: a real artist's face or voice, readable
  logos, an unwanted second vocal, or a second genre's palette leaking in.
- Avoid direct imitation of living artists or directors; describe observable
  craft traits (one governing visual rule, structural bookends, contained
  staging).
- Keep genre recipes stereotype-safe: default to positive, progressive
  performer framing; do not reproduce objectifying legacy tropes.

## Self-check

Before returning the prompt, verify:

1. The format is named and its address mode is respected.
2. The song map assigns one primary event and a visible end state per section.
3. The beat contract names the audio events and their visual relationship.
4. Restraint, repetition, counterpoint or escalation is justified by the supplied
   track/brief; no chorus, bridge or energy change is invented.
5. The audio treatment is explicit: native brackets or the black-sync `@Video 1`
   timing authority — the song never enters as a bare `@Audio 1` input.
6. Lip-sync lines appear verbatim in `{...}` and match the master inside the
   black-sync `@Video 1` container.
7. Dense vocal coverage uses per-line cues; required timing is verified from audio
   or explicitly unresolved. Raw evidence and simplified prompt windows stay separate.
8. **If `generate_audio` is true:** the prompt accounts for re-performance risk
   (section 3a) and the timestamped timeline is used when coverage is critical.
9. Each proposed generation uses a live-supported natural duration, or is an
   explicit continuous one-take.
10. The genre lock is a single recipe, not a mixture.
11. The style seal is compact and does not contradict the format or genre.
12. The response contains the prompt, not an unrelated production workflow.
