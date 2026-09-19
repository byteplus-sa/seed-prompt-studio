---
description: Sync prompt-composition skills and referenced contracts from the sibling ark-director checkout
agent: build
---

# Sync skills from ark-director

Sync this repo's prompt-composition skills from the sibling `ark-director`
checkout. Work from this repo's root. Never commit — leave all changes for
review.

## Scope

**Allowlist — sync only these 25 skills:**

`brief-intake`, `prompt-review`, `seedance-prompt-25`,
`seedance-prompt-25-filipino`, `seedance-prompt-20`,
`seedance-acting-console`, `seedance-animation-styles`,
`seedance-camera-presets`, `seedance-graybox-world`, `seedance-lens-presets`,
`seedance-lighting-presets`, `seedance-pacing-presets`,
`seedance-motion-design`, `seedance-music-video`, `seedance-restoration`,
`seedance-vfx-prompt`, `seedream-prompt`, `seedream-character-sheet`,
`seedream-location-asset`, `seed-audio-prompt`, `ugc-ad-modes`,
`ugc-motion-presets`, `color-grade-palettes`, `tig-blocking-map`,
`tig-scene-engine`

**Referenced contracts — sync these 5 files:**

`rules.json`, `production-policy.md`, `seedance-reference.md`,
`element-identification.md`, `audio-video-alignment.md`

**Never sync:** `template-factory` (a deliberate prompt-only fork whose content
diverges from ark-director) and every skill not on the allowlist.

## Arguments

`$ARGUMENTS` — optional space-separated skill names. When present, sync only
those skills (each must be on the allowlist; reject any that is not). Empty
means sync all 25.

## Procedure

### 1. Verify the source checkout

```bash
SRC="../ark-director"
test -f "$SRC/.agents/skills/seedance-prompt-25/SKILL.md" && test -f "$SRC/AGENTS.md" \
  && echo "source ok: $SRC" \
  || { echo "ERROR: ark-director checkout not found at $SRC — stop and report"; exit 1; }
```

If the check fails, stop and report. Do not sync from anywhere else.

### 2. Diff and sync each allowlisted skill

Set `SKILLS` to the allowlist, or to `$ARGUMENTS` when provided. Then:

```bash
for s in $SKILLS; do
  if diff -rq --exclude='.DS_Store' --exclude='__pycache__' \
      "$SRC/.agents/skills/$s" ".agents/skills/$s" >/dev/null 2>&1; then
    echo "unchanged  $s"
  else
    echo "updated    $s"
    diff -rq --exclude='.DS_Store' --exclude='__pycache__' \
      "$SRC/.agents/skills/$s" ".agents/skills/$s" 2>/dev/null | sed 's/^/    /'
    rsync -a --delete --exclude='.DS_Store' --exclude='__pycache__' \
      "$SRC/.agents/skills/$s/" ".agents/skills/$s/"
  fi
done
```

`rsync --delete` mirrors the source bundle, so renamed or removed reference
files disappear here too. The destination skill directory must already exist;
never create a new skill directory from a sync.

### 3. Sync the referenced contracts

```bash
for c in rules.json production-policy.md seedance-reference.md \
         element-identification.md audio-video-alignment.md; do
  if cmp -s "$SRC/.agents/contracts/$c" ".agents/contracts/$c"; then
    echo "unchanged  contracts/$c"
  else
    echo "updated    contracts/$c"
    cp "$SRC/.agents/contracts/$c" ".agents/contracts/$c"
  fi
done
```

### 4. Verify the result

Frontmatter names must match their directories:

```bash
python3 - <<'PY'
import re, pathlib
ok = True
for p in sorted(pathlib.Path('.agents/skills').glob('*/SKILL.md')):
    fm = re.match(r'\A---\s*\n(.*?)\n---(?:\n|$)', p.read_text(), re.DOTALL)
    if not fm:
        print(f'NO FRONTMATTER: {p}'); ok = False; continue
    name = re.search(r'^name:\s*(\S+)', fm.group(1), re.M)
    if not name or name.group(1) != p.parent.name:
        print(f'NAME MISMATCH: {p}'); ok = False
print('frontmatter ok' if ok else 'frontmatter problems above')
PY
```

Relative links must resolve. Two dangling links are the known baseline
(`element-identification.md` and `production-policy.md` point at the
html-graphic-render schema, which this workspace intentionally omits). Report
anything beyond those two:

```bash
python3 - <<'PY'
import re, pathlib
root = pathlib.Path('.').resolve()
bad = []
for doc in root.rglob('*.md'):
    if '.git' in doc.parts:
        continue
    for n, line in enumerate(doc.read_text().splitlines(), 1):
        for raw in re.findall(r'\]\(([^)]+)\)', line):
            if '://' in raw or raw.startswith(('#', 'mailto:')):
                continue
            rel = raw.split('#')[0].strip('<>')
            if rel and not (doc.parent / rel).resolve().exists():
                bad.append(f'{doc.relative_to(root)}:{n} -> {raw}')
print(f'dangling links: {len(bad)} (known baseline: 2)')
for b in bad:
    print(' ', b)
PY
```

```bash
find .agents -name '.DS_Store' -o -name '__pycache__' | wc -l
```

### 5. Report

Summarize as a table: skill or contract | unchanged / updated | files that
changed. Then:

- If any synced `SKILL.md` changed its frontmatter description, list those
  skills and ask whether the README skill-table row should be updated. Do not
  rewrite README rows automatically — several rows are deliberately reworded
  for this prompt-only workspace.
- State that nothing was committed and list the modified paths.
- If nothing changed, say so plainly.
