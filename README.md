# AIRAC Data Archive

Long-term version-controlled archive of prepared input files for each AIRAC cycle, used by the [VFPC](https://github.com/VFPC) ecosystem.

Files are prepared by [airac-data-fetcher](https://github.com/VFPC/airac-data-fetcher) and staged here for manual review before committing.

---

## Repository structure

Each cycle occupies its own subdirectory:

```
vFPC 2601/
  vFPC 2601.zip    ← all input files for this cycle
  manifest.md      ← archive metadata
vFPC 2602/
  vFPC 2602.zip
  manifest.md
vFPC 2603/
  ...
```

---

## Contents of each zip

Every archive contains exactly these seven files:

| File | Description |
|------|-------------|
| `Routes.csv` | SRD route data — primary input to New-SRDParser |
| `Notes.csv` | SRD notes sheet |
| `EG-ENR-3.2-en-GB.html` | UK eAIP ENR 3.2 — input to AIP-Parser |
| `EG-ENR-3.3-en-GB.html` | UK eAIP ENR 3.3 — input to AIP-Parser |
| `UK_YYYY_NN.sct` | VATSIM UK sector file |
| `in.json` | Parser configuration (carried forward from the previous cycle) |
| `out.json` | SRD Parser output |

Raw Excel files (`.xlsx`) are not archived — the CSVs are the downstream-ready outputs.

---

## Manifest format

Each `manifest.md` records:

- Cycle ident, effective date, expiry date
- Date and user who created the archive
- List of files included

Example:

```markdown
# Archive manifest — vFPC 2603

**Cycle:** 2603
**Effective:** 2026-03-19
**Expires:** 2026-04-15
**Archived:** 2026-03-19 14:32:07 UTC
**Archived by:** jkino

## Files

- `EG-ENR-3.2-en-GB.html`
- `EG-ENR-3.3-en-GB.html`
- `Notes.csv`
- `Routes.csv`
- `UK_2026_03.sct`
- `in.json`
- `out.json`
```

---

## How to add a new cycle archive

Archives are created by [airac-data-fetcher](https://github.com/VFPC/airac-data-fetcher). The full workflow is:

```
# 1. Fetch all source files into the working directory
python -m src fetch --cycle 2603

# 2. Run New-SRDParser — produces out.json

# 3. Stage the archive (requires a local clone of this repo)
python -m src archive --cycle 2603

# 4. Review what was staged
git status
git diff --cached

# 5. Commit and push
git commit -m "Add vFPC 2603 archive"
git push
```

The `archive` command writes the zip and manifest and runs `git add`, but never auto-commits. You always review before committing.

---

## Related repos

| Repo | Purpose |
|------|---------|
| [VFPC/airac-data-fetcher](https://github.com/VFPC/airac-data-fetcher) | Downloads and prepares the files that end up in these archives |
| [VFPC/New-SRDParser](https://github.com/VFPC/New-SRDParser) | Reads `Routes.csv`, `.sct`, `in.json` → produces `out.json` |
| [VFPC/AIP-Parser](https://github.com/VFPC/AIP-Parser) | Reads `EG-ENR-3.2-en-GB.html` and `EG-ENR-3.3-en-GB.html` |
