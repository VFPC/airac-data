# in.json Notes Audit — 2603 AIRAC

**Started:** 2026-03-02  
**Source audit:** `Documentation/notes/InJson_Notes_Audit.md` (in code repo)  
**File being corrected:** `in.json` in this directory  
**SRD source:** UK and Ireland SRD Notes — 19 Feb 2026 AIRAC (2602)

---

## Section A — FL Value Errors

### Note 113
- **SRD says:** Route via NIRIF "only available for traffic inbound to Dublin Group when danger area activity is promulgated in EGD201F/G or their associated FBZs above FL145 and the standard/alternative routing via M17/Q63 VATRY is unavailable for flight planning at the filed FL."
- **in.json has:** min=305 (unconditional) + max=305 across overnight/weekend time windows
- **Decision:** KEEP AS-IS with comment added (2026-03-02).
  - The FL145 in the SRD refers to the danger area activation level, not a route FL constraint.
  - On Vatsim, danger areas are not used, so the trigger condition can never be true — this route is effectively never available.
  - Rather than remove the route (which creates ATC/pilot conflicts for beginner controllers), it is coded as FL305 only, overnight/weekend windows, making it practically unusable while remaining technically present.
  - A `comment` field has been added to the first note 113 entry in `in.json` explaining this reasoning for future maintainers.

### Note 124
- **SRD says:** "FL250-280 MOGLI-MAMUL not available on this route unless inbound to EGNT/NV/PD. Operators advised to file via POL if below FL285."
- **in.json had:** max=245
- **Decision:** CORRECTED to max=250 (2026-03-02). FL250 is the precise hard boundary stated by the SRD — a TRA/airspace limit, not a cruise level. The ±5 adjustment was unnecessary.

### Note 229
- **SRD says:** "maximum level of FL70"
- **in.json had:** max=75
- **Decision:** CORRECTED to max=70 (2026-03-02). FL70 is the precise hard ceiling stated by the SRD. The ±5 adjustment was unnecessary.

### Note 397
- **SRD says:** "UTFAV only available for EGGW/EGKK departures with RFL345+"
- **in.json had:** min=245
- **Decision:** CORRECTED to min=345 (2026-03-02). Was a digit transposition error (245 vs 345). FL345 is the precise minimum stated by the SRD.

### Note 423
- **SRD says:** "FL190 and Below" (route for traffic flying below TRA008)
- **in.json had:** max=195
- **Decision:** CORRECTED to max=190 (2026-03-02). FL190 is the precise TRA008 boundary. The ±5 adjustment was unnecessary.

### Note 425
- **SRD says:** "when TRA008 is active this route is available for traffic FL195-FL240"
- **in.json had:** max=245
- **Decision:** CORRECTED to max=240 (2026-03-02). FL240 is the precise TRA008 upper boundary. The ±5 adjustment was unnecessary.

**General note on 124/229/423/425:** These ±5 FL adjustments were originally made in an attempt to account for odd/even semi-circular rule levels. This was incorrect — these FL values are hard airspace/TRA boundaries, not cruise level suggestions. The semi-circular rule does not apply to these constraints. All corrected to match SRD exactly.

---

## Section B — Phantom Notes

### Note 446 (suspicious)
- **Status:** Present in in.json with real destination logic (dests: EIDG/EIDW/EIME/EIWT, min=335 for nodests), but note ID does not exist in SRD (skips 445→447)
- **Decision:** REMOVED from 2603 in.json (2026-03-02). Note 446 was removed from the SRD at some point and was not cleaned up from in.json at that time. Both entries deleted.

### Notes 106, 990, 991 (intentional)
- **Note 106:** **REMOVED from 2603 in.json** (2026-03-02). Note coding was deliberately removed June 29, 2023 per GitHub issue #96. No longer exists in the SRD worksheet. Tombstone entry removed — `audit_decisions.md` is the record of why it's gone.
- **Note 117:** **REMOVED from 2603 in.json** (2026-03-02). Tombstone entry ("Note coding removed June 29, 2023 per issue #96"). However, note 117 is still referenced on **263 active SRD routes** (L/UL9 KONAN UL607) while the notes worksheet contains no body text for it — only the heading. This is a NATS data quality issue: pilots and ATC cannot know what restriction applies. Added to `NATS_FL_Conflict_Report.md` for reporting.
- **Notes 990 & 991:** **REMOVED from 2603 in.json** (2026-03-02).
  - 990 had 14 entries (time-windowed, Mon–Sun morning and evening slots, no ban/warn/min/max)
  - 991 had 8 entries (Mon–Sun early morning slots, no ban/warn/min/max)
  - Both were workarounds for the old parser. The new parser handles whatever these were papering over correctly.
  - No equivalent note IDs exist in the SRD CSV — these were always synthetic/custom.
  - As of 2603, the new parser is production. These entries are dead and removed.

### Notes 120, 241–247, 329, 331, 353, 420, 462, 473, 474
- **Status:** All had real active coding (time windows, alerts, region conditions) but cross-checking confirmed: none appear in the 2602 SRD notes worksheet and none are referenced on any routes in the 2602 SRD CSV. Coding from a previous AIRAC — note text and route references were removed from the SRD at some point but the in.json entries were never cleaned up.
- **Decision:** REMOVED from 2603 in.json (2026-03-02). Detail of what was removed:
  - 120: 5 time window entries (Mon–Fri overnight 1700–0900)
  - 241: 1 time window entry (Fri 1600–Sun 0800)
  - 242: 1 time window + alt text entry (Fri 1700–Sun 0800, "CDR2 at other times")
  - 243: 1 time window entry (Fri 1600–Sun 0800)
  - 244: 1 time window entry (Fri 1600–Sun 0800)
  - 245: 1 time window entry (Fri 1600–Sun 0800)
  - 246: 1 time window entry (Fri 1600–Sun 0800)
  - 247: 1 time window entry (Fri 1600–Sun 0800)
  - 329: 1 time window entry (2000–0600)
  - 331: 1 time window entry (0001–0600)
  - 353: 5 time window entries (weekday mornings + Fri evening–Sun)
  - 420: 5 time window entries (Mon–Fri overnight 1800–0800)
  - 462: Warn alert "Only available when the military are on holiday"
  - 473: Ban alert "Not available until further notice"
  - 474: Region condition (D201F active)

---

## Section C — Missing Notes

### Note 520
- **SRD says:** "When EGD509 Active" (note: likely typo for EGD509 in the worksheet)
- **Referenced on:** 20 routes (combined with note 521)
- **Decision:** DELIBERATELY NOT CODED (2026-03-02). The trigger condition (EGD509 danger area active) is a real-world ATC/military activation that never occurs on Vatsim. The constraint is unmodelable. The 20 affected routes will have no note constraint applied, which is correct behaviour for Vatsim.

### Note 521
- **SRD says:** "When EGD509 NOT ACTIVE" — followed by contingency scenarios S223–S242 (EGCON series): FL restrictions and routing changes for various airport groups when specific EGCON contingency plans are implemented.
- **Referenced on:** 20 routes (combined with note 520)
- **Decision:** DELIBERATELY NOT CODED (2026-03-02). The trigger conditions (EGD509 inactive + specific EGCON contingency scenarios active) require real-world ATC contingency activation that never occurs on Vatsim. The scenarios involve military airspace management that is outside the scope of Vatsim UK operations. Same rationale as note 113 (danger area trigger unmodelable). The 20 affected routes will have no note constraint applied, which is correct behaviour for Vatsim.

---

---

## Process Notes for Next Month's Audit

### What worked well
- Reading the full note entries from `in.json` before making any decisions — the original audit summary was wrong about several notes (e.g. called 120, 241–247 etc. "no-ops" when they had real coding).
- Cross-checking each note three ways: (1) does it exist in the SRD notes worksheet, (2) is it referenced on any routes in the SRD CSV, (3) what does the in.json coding actually do.
- Using `findstr /r "Notes:.*[ -]NNN[^0-9]"` for exact note reference matching in the CSV — the naive `findstr "Notes: 120"` matches FL values and coordinates as false positives.

### What to do differently next time
- **Run the cross-check first, before the audit doc is written.** The audit was generated from a snapshot of the file that didn't match the current state. Always read from the live file.
- **Check for tombstone entries early.** Notes 106 and 117 both had `"comment": "Note coding removed..."` — scan for all such entries at the start and batch-remove them.
- **When a note has no SRD worksheet text AND 0 route references, it is safe to remove regardless of what's coded.** No need to analyse the coding — it can never fire.
- **The semi-circular rule does NOT apply to FL constraints in notes.** SRD FL values in notes are hard airspace/TRA boundaries. Code them exactly as stated. Do not adjust ±5.
- **"Unmodelable" is a valid and correct decision.** Notes triggered by danger area activation, EGCON contingency scenarios, or other real-world ATC conditions that don't exist on Vatsim should be deliberately omitted and documented — not approximated with arbitrary FL restrictions (unless there is a specific Vatsim-context reason to do so, as with note 113).
- **Check for notes removed from the SRD but still referenced on routes** (like note 117). These are NATS data quality issues and should go in the NATS report.
- **990/991 pattern** — if synthetic/custom note IDs ever appear again, confirm they have 0 SRD route references before removing. They should, but verify.

### Useful shell commands for next audit
```powershell
# Find exact note references in SRD CSV (replace NNN with note number)
findstr /r "Notes:.*[ -]NNN[^0-9]" "C:\Users\jkino\Desktop\SRD Testing Files\UK and Ireland SRD_Excel and Notes_19 Feb 2026 AIRAC.csv"

# Count routes referencing a note
(findstr /r "Notes:.*[ -]NNN[^0-9]" "...\UK and Ireland SRD_Excel and Notes_19 Feb 2026 AIRAC.csv" | Measure-Object -Line).Lines

# Batch check multiple notes at once
foreach ($n in @(120, 241, 242)) { $c = (findstr /r "Notes:.*[ -]$n[^0-9]" "...\SRD.csv" | Measure-Object -Line).Lines; Write-Host "Note $n`: $c routes" }

# Find all tombstone entries in in.json
Select-String -Path "in.json" -Pattern '"comment".*removed'
```

---

## Changes Made to in.json

| Date | Note(s) | Change |
|------|---------|--------|
| 2026-03-02 | 990, 991 | Removed all 22 entries (14 × note 990, 8 × note 991) — old-parser workarounds, dead in new parser |
| 2026-03-02 | 106 | Removed tombstone entry — note coding deliberately removed 2023-06-29 per GitHub issue #96; no longer in SRD |
| 2026-03-02 | 113 | No coding change — added comment to first entry explaining FL305 is a deliberate Vatsim approximation (danger area trigger unmodelable) |
| 2026-03-02 | 124 | max: 245 → 250 (match SRD exactly; ±5 semi-circular adjustment was incorrect) |
| 2026-03-02 | 229 | max: 75 → 70 (match SRD exactly; ±5 semi-circular adjustment was incorrect) |
| 2026-03-02 | 397 | min: 245 → 345 (digit transposition fix; SRD says RFL345+) |
| 2026-03-02 | 423 | max: 195 → 190 (match SRD exactly; ±5 semi-circular adjustment was incorrect) |
| 2026-03-02 | 425 | max: 245 → 240 (match SRD exactly; ±5 semi-circular adjustment was incorrect) |
| 2026-03-02 | 446 | Removed both entries (dests + min/nodests) — note removed from SRD at some point, not cleaned up from in.json at the time |
| 2026-03-02 | 117 | Removed tombstone entry — note coding deliberately removed 2023-06-29 per GitHub issue #96 (same as note 106) |
| 2026-03-02 | 120, 241–247, 329, 331, 353, 420, 462, 473, 474 | Removed all entries (31 total) — not in SRD notes worksheet, not referenced on any SRD routes; coding from a previous AIRAC never cleaned up |

---

## Fixes Section — Coordinate Additions

A sentinel check was added to `Program.cs` that warns on any fix remaining at (0,0) after
the full data load. This check found 10 fixes in the `exits` section of in.json that had no
coordinates in either the UK SCT file or the `fixes` section of in.json. The parser's
`InJsonLoader` assigns (0,0) as a placeholder when a fix is not in the SCT file; the `fixes`
section is supposed to provide real coordinates for these, but 9 were missing.

All 9 were either Irish airports (not in UK SCT) or oceanic entry points on the western
boundary of UK controlled airspace.

**Fixes added to the `fixes` section (both production 2602 and 2603 in.json):**

| Fix | Type | Latitude | Longitude | Source |
|-----|------|----------|-----------|--------|
| EICK | Airport (Cork) | 51.8413 | -8.4911 | SkyVector |
| EIKN | Airport (Ireland West Knock) | 53.9103 | -8.8186 | SkyVector |
| EIKY | Airport (Kerry) | 52.1809 | -9.5238 | SkyVector |
| EINN | Airport (Shannon) | 52.7020 | -8.9248 | SkyVector |
| ADARA | Oceanic entry point | 51.0000 | -15.0000 | OpenNav |
| LIMRI | Oceanic entry point | 52.0000 | -15.0000 | OpenNav |
| MALOT | Oceanic entry point | 53.0000 | -15.0000 | OpenNav |
| NEBIN | Oceanic entry point | 54.0000 | -15.0000 | OpenNav |
| TOBOR | Oceanic entry point | 55.0000 | -15.0000 | OpenNav |

**N92** remains at (0,0) — it is an airway name that was mistakenly added as a SID point in
the airports section of in.json. It has zero route references and produces no constraints.
It is harmless but should be investigated and removed in a future cleanup.

### Why this matters

The direction pipeline calculates a magnetic bearing between two fixes to determine whether a
route is eastbound (odd FLs) or westbound (even FLs). A fix at (0,0) — the Gulf of Guinea —
would produce a bearing of approximately due south from the UK, which could result in an
incorrect direction assignment.

In practice, all 9 affected fixes had `dir` overrides in the `exits` section, so the bearing
calculation was never reached — the overrides took precedence. But this was fragile: if an
override were ever removed, the (0,0) bearing would silently produce wrong directions with no
warning in the logs.

The sentinel check in `Program.cs` is permanent and runs every production cycle. If a future
in.json update introduces a new exit point or SID point that is not in the SCT file, the
warning will appear in the production log, prompting the maintainer to add the fix to the
`fixes` section.

### How to maintain the fixes section

When adding a new exit point to the `exits` section of in.json:
1. Run the parser
2. Check the production log for `(0,0)` warnings
3. If the new fix appears, find its real coordinates (SkyVector for airports, OpenNav for waypoints)
4. Add it to the `fixes` section of in.json
5. Rerun and confirm the warning is gone
