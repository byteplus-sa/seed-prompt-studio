# Frame extraction (analysis-only)

When a local extraction tool is available, the agent extracts frames itself
instead of asking the user for screenshots. Extraction is a **read-only
analysis step**: it produces scratch frames, never deliverables, never
references, and nothing is uploaded.

## When this mode applies

Check availability first:

```bash
command -v ffmpeg >/dev/null && command -v ffprobe >/dev/null \
  && echo "extraction available" || echo "extraction unavailable"
```

If unavailable, fall back to the user keyframe set or the external pass.

## Procedure

```bash
VIDEO="<path to the user-provided video>"
FRAMES="projects/<project>/frames"
mkdir -p "$FRAMES"
```

### 1. Probe the source

```bash
ffprobe -v error -show_entries format=duration -of default=nw=1:nk=1 "$VIDEO"
ffprobe -v error -select_streams v:0 \
  -show_entries stream=width,height,r_frame_rate -of default=nw=1 "$VIDEO"
```

Record duration, resolution, and frame rate. These are **measured**, not
estimated.

### 2. Detect shot boundaries

```bash
ffmpeg -v info -i "$VIDEO" -vf "select='gt(scene,0.3)',showinfo" \
  -fps_mode vfr "$FRAMES/cut_%04d.png" -y 2>&1 | grep -o "pts_time:[0-9.]*"
```

Each `pts_time` is a measured cut. Use `scene=0.2` for subtle cuts,
`0.4–0.5` when only hard cuts should count. Add `0.0` as the first boundary
and the probed duration as the last; shots are the intervals between them.

### 3. Extract per-shot frames

For each shot, extract start, middle, and end frames by timestamp:

```bash
ffmpeg -v error -ss <start_s> -i "$VIDEO" -frames:v 1 "$FRAMES/shot01_a.png" -y
ffmpeg -v error -ss <mid_s>   -i "$VIDEO" -frames:v 1 "$FRAMES/shot01_b.png" -y
ffmpeg -v error -ss <end_s>   -i "$VIDEO" -frames:v 1 "$FRAMES/shot01_c.png" -y
```

### 4. Extract motion bursts

For motion-critical shots, extract a short dense burst across the shot:

```bash
ffmpeg -v error -ss <start_s> -i "$VIDEO" -t <duration_s> -vf fps=6 \
  "$FRAMES/shot01_m%02d.png" -y
```

Read consecutive burst frames together to observe direction, speed,
amplitude, and easing. Motion between sampled frames remains an inference —
say so when confidence is not high.

## Budget

- Read frames in small batches; keep the total working set near 30–40 images.
- Three frames per shot for the breakdown; add a 6-frame burst only for
  motion-critical shots (or all shots when the video is short).
- For long videos, analyze one scene at a time and summarize before moving on.

## Boundaries

- Frames are analysis scratch under `projects/<project>/frames/`. They are
  never uploaded, never listed as references, and never bound as `@Image N`
  inputs. Delete them or leave them local; they are not deliverables.
- Extraction never uses filters, effects, transcoding, or assembly — only
  `ffprobe` inspection and plain frame extraction.
- The agent still has not *watched* the video continuously. Timing is measured;
  motion and audio observations remain sampled inferences.
- **Audio cannot be heard from frames.** Ask the user for a transcript or
  audio notes. Without them, set the analysis audio fields to explicit
  unknowns (`mode: "unknown (no audio access)"`, null music and dialogue,
  empty sfx) and flag the package audio-unverified — never guess.
