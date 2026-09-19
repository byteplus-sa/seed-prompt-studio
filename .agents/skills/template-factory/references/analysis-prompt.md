# Analysis Prompt — video breakdown

The canonical analysis prompt. It turns "understand this video" into a
reproducible, slot-complete `VideoBreakdown` that downstream prompts consume
word for word.

## Brand and identity mode

Choose one mode before running the analysis prompt. Record the mode in the
breakdown metadata.

| Mode | When | Brand / logo treatment | People |
| --- | --- | --- | --- |
| **De-identify (default)** | Brand unknown, unauthorized, or style-only replication | Replace brand text and logos with placeholder descriptions | Describe appearance only; never name or likeness-match |
| **Authorized real brand** | User supplies or authorizes a specific brand/product | Preserve real brand and product identity in descriptors so the user can source official assets | Still de-identify real people unless the user explicitly supplies approved talent identity |

## Execution modes

| Mode | How |
| --- | --- |
| **Agent video pass** | Your client can watch the video: run the prompt below yourself and return JSON only |
| **Keyframe set** | You cannot watch the video: read the user-provided frames as images, apply the same contract, mark timing/audio/motion as estimates, and set `source: keyframes` in metadata |
| **External pass** | Hand the prompt to the user's video-capable tool; they return the JSON for validation |

Never claim to have watched a video you could not access.

```text
You are a film-analysis expert. Analyze the attached video (or keyframe set)
and produce a JSON object that fully describes how to REPRODUCE its style,
composition, and grammar.

RULES
- Output valid JSON only. No prose outside the JSON.
- People: describe a person's appearance (age, build, hair, clothing) but NEVER
  name them, infer a real identity, or likeness-match a celebrity unless the
  caller explicitly authorized a supplied talent identity.
- Brands (apply the selected mode):
  - De-identify mode: NEVER reproduce text/logos that identify real brands.
    Replace brand text with a placeholder description.
  - Authorized real brand mode: preserve the authorized brand and product
    identity in element descriptors (exact logo/packshot cues). Do not invent
    alternate brand marks.
- Say what you want positively; describe what IS shown, not what to avoid.
- Break the video into consecutive shots at every cut. Each shot needs:
  index, start_s, end_s, duration_s, composition, camera, action, lighting,
  audio, and end_state (observable state at the shot's end).
- Use positive, unique shot indices starting at 1. Keep shots ordered,
  non-overlapping, and within the measured source duration. Set duration_s to
  end_s minus start_s. When working from keyframes, estimate boundaries from
  the user-provided duration and mark them as estimates.
- Identify every distinct character, location, and prop that appears. Assign
  each a short kebab-case id and an exhaustive visual descriptor (the exact
  phrasing a generator can use word-for-word). Mark the shot index of the
  clearest keyframe for each element.
- Make each keyframe_index and in_shots entry refer to an existing shot. A
  keyframe must also appear in that element's in_shots list.
- Extract visual_style (grade, lighting_direction, lens, film_look), camera
  (shot_sizes, moves, framing, transitions), and audio (mode, music, sfx,
  dialogue — transcribe any dialogue verbatim inside {braces}).

Return this schema:
{
  "schema_version": "1.0",
  "title": string,
  "genre": string,
  "visual_style": {...},
  "camera": {...},
  "audio": {...},
  "elements": [{ "type": "character|location|prop|screen", "id", "tag",
                 "descriptor", "keyframe_index": [int], "in_shots": [int] }],
  "shots": [{ "index", "start_s", "end_s", "duration_s", "composition",
              "camera", "action", "lighting", "audio", "end_state" }]
}
```

## Validation

Validate the returned JSON against [breakdown-schema.json](breakdown-schema.json)
by inspection before presenting it:

- Valid JSON with all required fields and no extra fields.
- Shots ordered, non-overlapping, indices starting at 1, `duration_s` equal to
  `end_s - start_s`.
- Every `keyframe_index` and `in_shots` entry refers to an existing shot; each
  element's keyframe appears in its `in_shots`.
- Element ids are kebab-case and unique.

On a parse failure, ask for the JSON again with "return JSON only, no markdown
fences". On validation failures, list the exact findings as repair
constraints. Never proceed with an invalid breakdown.
