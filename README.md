# AIRAC Data Archive

Long-term version-controlled archive of prepared input files for each AIRAC cycle, used by the [VFPC](https://github.com/VFPC) ecosystem.

Files are prepared by [airac-data-fetcher](https://github.com/VFPC/airac-data-fetcher) and archived by [airac-archiver](https://github.com/VFPC/airac-archiver), then staged here for manual review before committing.

---

## Repository structure

Each cycle occupies its own subdirectory with flat files:

```
vFPC 2601/
  Routes.csv
  Notes.csv
  UK_2026_01.sct
  in.json
  out.2601.1.json
  manifest.md
vFPC 2602/
  ...
```

---

## Archived files

Each archive contains these allowlisted files:

| File | Description |
|------|-------------|
| `Routes.csv` | SRD route data — primary input to New-SRDParser |
| `Notes.csv` | SRD notes sheet |
| `UK_YYYY_NN.sct` | VATSIM UK sector file |
| `in.json` | Parser configuration (carried forward from the previous cycle) |
| `out.{ident}.{n}.json` | Versioned SRD Parser output (e.g. `out.2603.1.json`) |

All other files in the working directory (ENR HTMLs, logs, Excel source, etc.) are silently ignored by the archiver's allowlist.

### Versioned out.json

The parser writes `out.json`. During archival it is renamed to `out.{ident}.{n}.json` where `{ident}` is the 4-digit AIRAC cycle and `{n}` is an auto-incrementing version number. Re-archiving the same cycle preserves all previous versions and increments `n`.

---

## Manifest format

Each `manifest.md` records:

- Cycle ident, effective date, expiry date
- Date and user who created the archive
- SHA256 checksums for every file
- Warnings for any expected files that were absent

Example:

```markdown
# Archive manifest — vFPC 2603

**Cycle:** 2603
**Effective:** 2026-03-19
**Expires:** 2026-04-15
**Archived:** 2026-03-19 14:32:07 UTC
**Archived by:** jkino

## Files

| File | SHA256 |
|------|--------|
| `Notes.csv` | `a1b2c3...` |
| `Routes.csv` | `d4e5f6...` |
| `UK_2026_03.sct` | `789abc...` |
| `in.json` | `def012...` |
| `out.2603.1.json` | `345678...` |
```

---

## How to add a new cycle archive

Archives are created by [airac-archiver](https://github.com/VFPC/airac-archiver). The full workflow is:

```
# 1. Fetch all source files into the working directory
python -m src fetch --cycle 2603    # (in airac-data-fetcher)

# 2. Run New-SRDParser — produces out.json

# 3. Stage the archive (in airac-archiver, requires a local clone of this repo)
python -m src archive --cycle 2603

# 4. Review what was staged
cd <this repo>
git status
git diff --cached

# 5. Commit and push
git commit -m "Add vFPC 2603 archive"
git push
```

The `archive` command copies flat files and manifest and runs `git add`, but never auto-commits. You always review before committing.

---

## Related repos

| Repo | Purpose |
|------|---------|
| [VFPC/airac-archiver](https://github.com/VFPC/airac-archiver) | Collects allowlisted files and stages them in this repo |
| [VFPC/airac-data-fetcher](https://github.com/VFPC/airac-data-fetcher) | Downloads and prepares the files that end up in these archives |
| [VFPC/New-SRDParser](https://github.com/VFPC/New-SRDParser) | Reads `Routes.csv`, `.sct`, `in.json` → produces `out.json` |
