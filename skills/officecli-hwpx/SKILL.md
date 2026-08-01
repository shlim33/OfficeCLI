---
name: officecli-hwpx
description: "Use this skill any time a .hwpx file is involved -- Korean HWPX word-processor documents (한글) as input, output, or both. This includes reading, viewing, editing, or creating .hwpx files: paragraphs, tables, images inside Korean documents. Trigger whenever the user mentions 'hwpx', '.hwpx', '한글 문서', 'HWPX', or references a .hwpx filename."
---

# OfficeCLI HWPX Skill

## ⚠️ Discover-First Rule

hwpx is a **format-handler plugin**, not one of officecli's three built-in formats — there is
no `help hwpx`. FIRST run `officecli plugins info officecli-hwpx` BEFORE guessing an element
path, a verb, or a prop name. That output is the schema (paths, verbs, props, vocabulary) and
it is pinned to the installed plugin version — when it disagrees with this file, **the plugin
is authoritative**.

```bash
officecli plugins info officecli-hwpx      # element/verb/prop vocabulary — run this first
```

## Workflow

Five steps. Every edit follows this shape.

1. **Locate the file.** Check `input/manifest.json` for the target path — never assume a
   filename.
2. **Orient.** `officecli view "$FILE" outline` — paragraph/table/image counts and structure.
   Never edit blind.
3. **Edit incrementally.** `set` / `add` / `remove` / `move`, one call at a time — check the
   exit code before stacking another. A multi-step script that fails at step 3 cascades
   silently if you don't check.
4. **Look at it.** `officecli view "$FILE" screenshot` — headless-rendered PNG comes back as an
   image you can see. This is the only way to catch overflow, misalignment, or a broken table
   that the structural verbs don't surface. Confirm the result before declaring the edit done.
5. **Deliver to `output/`.** Save the finished file to the session's output side, not
   `input/`.

## Token-Savings Rule

**This saves more context than the document's own size does.** NEVER dump `view text` in full
on a large document. Use `view outline` / `view stats` for structure only, then
`query paragraph|row|cell|image` to pull just the elements you actually need. A blind
`view text` on a 40-page document burns far more context than a structure-first pass plus a
handful of targeted `query` calls ever will.

## hwpx-Specific Pitfalls

| Pitfall | What happens |
|---|---|
| Units | Sizes and margins are in **HWPUNIT** (1mm ≈ 283) — not points, not twips |
| Columns | `/table[T]/column[K]` is a **logical column** (`colAddr`) — it is not physical cell order |
| Merge | A table with a vertical merge **rejects** row moves and column ops. Workaround: `cell --prop colSpan=1 --prop rowSpan=1` (split) → do the op → re-merge. ⚠️ the original size does **not** come back (한글's own "split cell" also splits evenly) |
| Color | Pass `#RRGGBB` — the on-disk `#BBGGRR` byte-swap is the plugin's job, never do it yourself |
| Crop | `crop*` props are **absolute**, not cumulative. An image with no `imgDim` rejects any crop request |
| Rotation | **Not supported.** Held back for lack of real-world rotated samples — do not attempt it |
