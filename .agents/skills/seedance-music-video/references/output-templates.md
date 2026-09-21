# Output Templates

Focused reference for `seedance-music-video`. Read [the entrypoint](../SKILL.md) for
mode selection and caller responsibilities.

- [Output formats](#output-formats)

## Output formats

### Music-video block

Use when the user only wants the directing layer:

```text
[Music-Video Format]
<Format name, direct or indirect address, and performance-to-atmosphere ratio.>

[Song Map]
Section 1 (<verified time range or event cue>): <visual assignment, energy, end state>.
Section 2 (<verified time range or event cue>): <visual assignment, energy, end state>.
Final Section (<verified time range or event cue>): <closing assignment and final visible state>.

[Beat & Cut Contract]
<Which audio events the cuts and camera land on; cut density per section.>

[Audio Treatment]
<Audio treatment: native audio brackets, or the black-sync @Video 1 timing authority — the song never enters as bare @Audio 1.>

[Genre Lock]
<Palette, lighting, camera grammar, motion cadence, and tone.>

[Style Seal]
<Compact closing sentence and relevant exclusions.>
```

### Full Seedance prompt

Use when the user asks for a complete prompt:

```text
[Timing & Soundtrack Authority] (only when the performance follows a master)
@Video 1 is the black-sync container — the master muxed into pure-black video.
It provides the exact soundtrack, beat timing, vocal rhythm, and all temporal
pacing. Match <performer>'s visible mouth only to the vocals in @Video 1.
(Prerequisite: the user prepares the container first — the song never enters
as a bare @Audio 1 input.)

[Reference Roles] (only when references exist)
@Image 1 defines <performer>'s <appearance, wardrobe, or identity>.
@Image 2 defines <scene or venue>. Do not use <unwanted content>.

<Subject performs the primary action in <scene>.>
The visuals feature <genre lock: palette, lighting, lens, grade, look>.
Use <shot sizes, camera moves, and cuts>, with <beat contract>.
Audio includes <(music)> <{sung lines}> <sound effects>.
Audio: <black-sync @Video 1 authority or native brackets>.

[Shot Plan] or [Stage Plan]
Shot 1 (<time range>): @Image <N> only — <location or subject>; <one event and
visible end state>.
Shot 2 (<time range>): @Image <N> only — <location or subject>; <one event and
visible end state>.
Final Shot (<time range>): @Image <N> only — <closing event and final visible
state>.
Scope each shot to its own references ("@Image N only") so no location or look
bleeds across a cut.

[Lip-Sync & Lyric Timing] (only when lip-synced vocals are requested)
<Performer>'s mouth shapes align to every syllable. Perform every line in full,
in order, at its beat slot — no line skipped, shortened, mumbled, or reordered.
<X>-line timestamped timeline (<start>s–<end>s): <[X-Ys] { line }> per line.

[Maintain Consistency]
Keep <performer identity, wardrobe, venue, camera grammar, and audio>
consistent across the section chain.

[Style Seal]
<Compact genre, palette, motion cadence, beat contract, and tone.>
```

Return the prompt directly. Do not add production workflow, tool selection,
asset management, approval gates, or generation instructions unless the user
explicitly asks for them.
