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

**Referenced contracts — synced on full runs only:**

`rules.json`, `production-policy.md`, `seedance-reference.md`,
`element-identification.md`, `audio-video-alignment.md`

**Never sync:** `template-factory` (a deliberate prompt-only fork whose content
diverges from ark-director) and every skill not on the allowlist.

## Arguments

`$ARGUMENTS` — optional space-separated skill names, or `all`. When names are
present, sync only those skills and skip the contract step; each name must be
on the allowlist. Empty or `all` syncs the full allowlist plus the contracts.

## Procedure

### 1. Preflight

```bash
SRC="../ark-director"
test -f "$SRC/.agents/skills/seedance-prompt-25/SKILL.md" && test -f "$SRC/AGENTS.md" \
  && echo "source ok: $SRC" \
  || { echo "ERROR: ark-director checkout not found at $SRC — stop and report"; exit 1; }
echo "source revision: $(git -C "$SRC" rev-parse --short HEAD)"
echo "--- this repo working-tree changes:"
git status --short
```

If the source check fails, stop and report. Never sync from another path. The
sync copies the source **working tree**, not the committed revision — the
scope-aware dirty check happens in step 3. If this repo has uncommitted
changes, warn that the sync diff will mix with them and recommend committing
or stashing first so the sync is reviewable.

### 2. Resolve the skill set

The bash blocks below share opencode's persistent shell session, so variables
set here are available to later steps.

```bash
ALLOWLIST="brief-intake prompt-review seedance-prompt-25 seedance-prompt-25-filipino seedance-prompt-20 seedance-acting-console seedance-animation-styles seedance-camera-presets seedance-graybox-world seedance-lens-presets seedance-lighting-presets seedance-pacing-presets seedance-motion-design seedance-music-video seedance-restoration seedance-vfx-prompt seedream-prompt seedream-character-sheet seedream-location-asset seed-audio-prompt ugc-ad-modes ugc-motion-presets color-grade-palettes tig-blocking-map tig-scene-engine"
ARGS="$ARGUMENTS"
[ "$ARGS" = "all" ] && ARGS=""
if [ -z "$ARGS" ]; then
  SKILLS="$ALLOWLIST"
  echo "full run: 25 skills + 5 contracts"
else
  SKILLS="$ARGS"
  for s in $SKILLS; do
    case " $ALLOWLIST " in
      *" $s "*) ;;
      *) echo "ERROR: $s is not allowlisted — stop and report"; exit 1 ;;
    esac
  done
  echo "scoped run: $SKILLS (contracts skipped)"
fi
```

### 3. Source scope check

The source working tree may be dirty with unrelated work. Confirm only when a
dirty path intersects the sync scope:

```bash
SCOPE_PATHS=""
for s in $SKILLS; do SCOPE_PATHS="$SCOPE_PATHS .agents/skills/$s"; done
[ -z "$ARGS" ] && SCOPE_PATHS="$SCOPE_PATHS .agents/contracts"
echo "--- dirty source paths intersecting the sync scope:"
git -C "$SRC" status --short -- $SCOPE_PATHS
```

- **Empty output** — proceed. Unrelated source changes cannot affect this
  sync; report them informationally in the summary.
- **Non-empty output** — list the intersecting files and ask the user to
  confirm before continuing. The sync would copy unreviewed in-progress work
  from ark-director.

### 4. Diff and sync each skill

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
files disappear here too. Every destination directory must already exist;
never create a new skill directory from a sync.

### 5. Sync the referenced contracts (full runs only)

```bash
if [ -z "$ARGS" ]; then
  for c in rules.json production-policy.md seedance-reference.md \
           element-identification.md audio-video-alignment.md; do
    if cmp -s "$SRC/.agents/contracts/$c" ".agents/contracts/$c"; then
      echo "unchanged  contracts/$c"
    else
      echo "updated    contracts/$c"
      cp "$SRC/.agents/contracts/$c" ".agents/contracts/$c"
    fi
  done
fi
```

### 6. Verify the result

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

No stray artifacts:

```bash
find .agents \( -name '.DS_Store' -o -name '__pycache__' \) | wc -l
```

### 7. Report

Summarize as a table: skill or contract | unchanged / updated | files that
changed. Then include:

- The source revision hash from the preflight.
- `git status --short` and `git diff --stat` so the user can review the sync
  diff directly.
- If any synced `SKILL.md` changed its frontmatter description, list those
  skills and ask whether the README skill-table row should be updated. Do not
  rewrite README rows automatically — several rows are deliberately reworded
  for this prompt-only workspace.
- State that nothing was committed. If nothing changed, say so plainly.
