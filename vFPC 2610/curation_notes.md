## AIRAC 2610 SRD-CF-001: JUSGE -> JUGSE spelling repair (20261001-192434)

Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2610\Routes.csv`
Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2610\Routes.csv.pre-srd-cf-001-jugse-spelling-fix.20261001-192434.bak`
Audit detail: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\_tmp_jugse_changes.json` (5 before/after row pairs)

### Problem

The EGSS SID/exit-fix field in the official NATS SRD `Routes.csv` export spells
the published fix name two different ways across its own rows:

- `JUGSE` (correct) — 7 rows
- `JUSGE` (misspelled, transposed letters) — 5 rows

3 of the 5 `JUSGE` rows are `MC` (airway-derived FL band) rows whose route
string begins `L6 DET ...`. New-SRDParser's MC pre-processor could not resolve
the airway-segment path `JUSGE -> DET` on `L6` because no fix named `JUSGE`
exists in any navdata source, producing:

```
MC unresolved [SEG_NOT_FOUND]: Airway L6: path JUSGE→DET not found in AIP data.
McPreProcessor: 4428/4431 resolved, 3 unresolved.
```

### Evidence that `JUGSE` is correct and `JUSGE` is the typo

- `UK_2026_10.sct` (VATSIM-UK sector file, AIRAC 2610): defines the fix as
  `JUGSE` (`N051.40.47.470 E000.18.39.890`) and uses `JUGSE` in the `L6`
  airway definition. No `JUSGE` fix exists anywhere in the sector file.
- `RAD_2610_v1_11.xlsx` (EUROCONTROL RAD workbook, AIRAC 2610): `JUGSE`
  appears 12 times across Annex 2B, Annex 3A DEP, and Annex 3B DCT
  (e.g. "EGSS shall file JUGSE SID", "DETJUGSE SID"). Zero occurrences of
  `JUSGE`.
- `Routes.csv` itself: 7 of its own 12 EGSS rows referencing this fix spell it
  `JUGSE`; only the 5 flagged rows spell it `JUSGE`. Two sibling rows with
  near-identical route strings (`BEVAV1J` vs `BEVAV1K`, same `L6 DET M604 LYD
  M189 NEVIL G27 ANGLO G273 BEVAV` routing) disagree on the spelling between
  each other, confirming a one-off data-entry transposition rather than an
  intentional distinct fix.

### Applied fix

Exact-line, exact-token replacement (`,JUSGE,` -> `,JUGSE,`) on exactly the
5 affected rows; no other occurrence of `JUSGE` existed anywhere else in the
file (verified before and after).

| Line | Before | After |
|---|---|---|
| 18608 | `EGSS,JUSGE,MC,175,L6 DET M604 LYD M189 NEVIL G27 ANGLO G273 BEVAV,BEVAV1J,EGJJ,` | `EGSS,JUGSE,MC,175,L6 DET M604 LYD M189 NEVIL G27 ANGLO G273 BEVAV,BEVAV1J,EGJJ,` |
| 18695 | `EGSS,JUSGE,65,75,DCT LAM,LOGAN2H,EGWU,` | `EGSS,JUGSE,65,75,DCT LAM,LOGAN2H,EGWU,` |
| 18832 | `EGSS,JUSGE,MC,175,L6 DET M604 LYD M189 WAFFU DCT,,SITET,` | `EGSS,JUGSE,MC,175,L6 DET M604 LYD M189 WAFFU DCT,,SITET,` |
| 18871 | `EGSS,JUSGE,245,265,L6 DET M604 LYD M189 WAFFU UM605,,XIDIL,` | `EGSS,JUGSE,245,265,L6 DET M604 LYD M189 WAFFU UM605,,XIDIL,` |
| 18872 | `EGSS,JUSGE,MC,245,L6 DET M604 LYD M189 WAFFU M605,,XIDIL,` | `EGSS,JUGSE,MC,245,L6 DET M604 LYD M189 WAFFU M605,,XIDIL,` |

Row count unchanged: edits only, no rows added/removed.

### Post-fix parser verification

Working copy refreshed from this source set to
`C:\Users\jkino\Desktop\SRD Testing Files` before re-running the parser.

- `McPreProcessor`: before `4428/4431 resolved, 3 unresolved (SEG_NOT_FOUND×3,
  airway L6)`; after **`4431/4431 resolved — 100%`**.
- `FixResolver` missing-fix warning for `JUSGE` (line -1, RawInput `JUSGE`):
  present before, **gone after**.
- Final constraint count: `13173` -> `13171` (net -2; two constraints merged
  once the route correctly resolved through the shared `JUGSE` fix instead of
  failing resolution).
- No new parser errors or warnings introduced by this change. The pre-existing
  `SctParser: Fix 'WTN' appears 2 times` warning and the in.json linter's
  `Notes.csv` header-regex failure (known issue from the airac-data-fetcher
  PR #27 trailing-column padding) are unrelated and still open for separate
  review.

### Comparison vs AIRAC 2609.1 (post-fix)

`scripts\compare_out_json.py` against
`airac-data\vFPC 2609\out.2609.1.json`:

- Total constraints: `12974` -> `13171` (net `+197`).
- `57` changed airports, `+852` / `-655` constraints.
- Full detail: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\comparison_report_2610.json`.

### Carry-forward

- Report to NATS as an SRD data-entry error (fix-name transposition:
  `JUGSE` -> `JUSGE` on 5 of 12 EGSS rows referencing the same published fix).
- Re-test every AIRAC until NATS corrects the rows; retire this curation entry
  once the official SRD source no longer contains the misspelling.
- Track in `srd_source_issue_ledger.json`.

## AIRAC 2610 Full Re-Curation Pass (SRD-CF-002 through SRD-CF-008)

Date: 2026-10-02. Applies the standing recurring SRD/RAD curation classes
(see irac_curation_ledger.md) to the fresh, uncurated AIRAC 2610
`Routes.csv` export, plus one newly-characterized confirmed-RAD-denial
family (`EG2983`). Carries forward the `full re-curation pass` item from
`airac_2610_rad_baseline.md`.

Row count: `32465` (post SRD-CF-001 JUGSE fix) -> `32134` (post this pass).

Tooling: `scripts/match_unexpected_denials_to_routes.py` (new; matches
`unexpected_denial` trace rows back to exact physical `Routes.csv` rows by
dep/SID/dest/normalized route/FL band -- normalizes away `DCT` and `<FRA>`
tokens, both of which are stripped in `out.json` route text but present in
source `Routes.csv`), `scripts/apply_routes_csv_removal.py` and
`scripts/apply_routes_csv_edit.py` (new; backup + multiset-matched
removal/edit + audit JSON), `scripts/check_eg_city_pair_caps.py` (re-pointed
at arbitrary AIRAC via `--airac`/`--routes-csv`/`--notes-csv`, previously
hard-coded to 2604), `scripts/generate_city_pair_cap_edit_spec.py` (new).

### SRD-CF-002: Confirmed RAD-denial packet family (re-applied)

Count: `75` rows. Rule clusters: `EG3499` (56), `EG3217` (16),
`EG2608` (1), `EG2628` (1), `EG2640` (1) -- matches the 2605-ledger
"Confirmed RAD-denial packet removals" family re-surfacing in the fresh 2610
export. 100% match rate (74 trace rows -> 75 physical rows; one trace route
had two physically-duplicate `Routes.csv` rows differing only by a literal
`DCT` vs `<FRA>` marker before the final waypoint -- both removed).

Backup: `Routes.csv.pre-srd-cf-002-confirmed-rad-denial-family.20261002-065120.bak`
Audit: `Routes.srd-cf-002-confirmed-rad-denial-family.20261002-065120.json`

Carry-forward: re-test every AIRAC; report to NATS; retire only when source
corrected (ledger precedent, irac_curation_ledger.md AIRAC 2605 section).

### SRD-CF-003: EG2983 DUBLIN_GROUP Q63-entry confirmed RAD-denial (new family)

Count: `46` rows, all `ARR EIDW`, branch indicator `'3.'` (`ARR
DUBLIN_GROUP` + `RFL ABV FL155`).

This looked at first like the already-ledgered 2606 "MEDOG DCT VATRY"
infeasibility family (same rule area, same final fix `VATRY`), but turned
out to be a distinct mechanism on investigation. All 46 routes enter airway
`Q63` at `NICXI` or `LANPI` and ride it to `VATRY`
(e.g. `...L9 NICXI Q63 VATRY`, `...MEDOG FOXLA NICXI LANPI Q63 VATRY`).
`EG2983`'s branch `'3.'` only excepts two routings to `VATRY` for
`DUBLIN_GROUP`/FL155+ traffic: explicit `VIA (LEMGU M17 VATRY)`, and an
RSA-gated `VIA (TIBGA Q63 VATRY)`. Neither exception names `NICXI` or
`LANPI`.

AIP segment check (`data/local/2610/aip/aip_segments.json`) confirmed
`Q63` is a real, continuous published airway
(`...AGCAT -> NICXI -> LANPI -> TIBGA -> VATRY`), so this is **not** an
illegal-ATS-route situation -- `NICXI`/`LANPI` are legitimately published
entry points onto `Q63`. But entering at `NICXI`/`LANPI` is a
physically longer/different routing than entering at `TIBGA`, and the RAD
text simply does not except the earlier entry points. Treated as a genuine
confirmed-RAD-denial (SRD published more `Q63` entry points toward
`VATRY` for `DUBLIN_GROUP` than the RAD authorizes), same curation
treatment as SRD-CF-002, not a Hub evaluator bug.

Backup: `Routes.csv.pre-srd-cf-003-eg2983-dublin-group-q63-entry.20261002-065131.bak`
Audit: `Routes.srd-cf-003-eg2983-dublin-group-q63-entry.20261002-065131.json`

Carry-forward: re-test every AIRAC; report to NATS as a new confirmed-RAD
finding (distinct from the 2606 MEDOG/VATRY ledger entry); retire only when
NATS corrects the published rows or the RAD exception list is widened.

### SRD-CF-004: EG2269 NIRIF/EVTOL cluster (investigated this session)

Count: `61` rows (58 distinct trace routes; 2 pairs of byte-identical
physical `Routes.csv` duplicates matched by `Min: MC` FL band). All
`ADES EIDW` via `...EVTOL N12 NIRIF`. Per
`Documentation/butler/staging/eg2269_nicxi_evtol_nirif_investigation_2026-10-02.md`:
`NIRIF` is default-unavailable and only becomes available via an exception
gated on danger areas `EGD201F/FZ1/G/GZ1` being ACT; under the
`vfpc-current` policy (all danger areas inactive), the exception never
fires and the default-deny stands. Decision: pull these routes from the SRD
now, continue toward an uploadable candidate, and revisit with more testing
later rather than block the release on it.

Backup: `Routes.csv.pre-srd-cf-004-eg2269-nirif-evtol.20261002-065145.bak`
Audit: `Routes.srd-cf-004-eg2269-nirif-evtol.20261002-065145.json`

Carry-forward: re-test every AIRAC; re-evaluate once the `restricted_area_active`-at-root
systemic audit (Linear `VFP-439`) is resolved, since `EG2269` was the
seed case for that audit.

### SRD-CF-005: Illegal ATS-route removals (re-applied)

Count: `12` rows. `EGNJ`/`EGXW` departures to
`GOREV`/`NIVUN`/`OSBON`/`PEMOS`/`PETIL`/`TINAC`/`VAXIT` via
`GOLES Y70 BATLI Y250 OBOXA P17 MEJAC`. Same family as the 2606 ledger
entry. AIP segment check (`data/local/2610/aip/aip_segments.json`)
reconfirmed `Y250` has no direct `BATLI -> OBOXA` segment -- the real
chain is `BATLI -> RIMTO -> KEFTE -> OBOXA -> GASKO`. The 2606 ledger's
other illegal-segment finding (`UNTAL N110`) is no longer present in the
2610 export (`0` occurrences) -- no action needed for it this cycle.

Backup: `Routes.csv.pre-srd-cf-005-illegal-ats-batli-y250-oboxa.20261002-065201.bak`
Audit: `Routes.srd-cf-005-illegal-ats-batli-y250-oboxa.20261002-065201.json`

Carry-forward: re-test every AIRAC against current AIP segment facts; report
to NATS; retire only when source corrected.

### SRD-CF-006: MEDOG DCT VATRY removals (re-applied)

Count: `137` rows -- identical count to the 2606 ledger entry. Direct
`MEDOG DCT VATRY` segment, rejected by IFPUV as too long for
`EGTTNFRAW:105:245` (`ROUTE165`). Same family, same evidence basis as
2606; re-applied unchanged to the fresh 2610 export.

Backup: `Routes.csv.pre-srd-cf-006-medog-dct-vatry-infeasible.20261002-065219.bak`
Audit: `Routes.srd-cf-006-medog-dct-vatry-infeasible.20261002-065219.json`

Carry-forward: re-test every AIRAC; report to NATS; retire only when source
corrected or IFPUV accepts a valid direct-route exception.

### SRD-CF-007: PEMOB DCT VATRY -> PEMOB M17 VATRY repairs (re-applied)

Count: `162` rows, scoped to the full
`MEDOG DCT KRAGY DCT PEMOB DCT VATRY` chain only (same scoping rule as
2606). Replaced the terminal `PEMOB DCT VATRY` fragment with
`PEMOB M17 VATRY`; left the `1` parked non-`KRAGY` `EGTO` row
untouched per the 2606 carry-forward note (no fresh evidence supports
broadening to that row this cycle).

Backup: `Routes.csv.pre-srd-cf-007-pemob-m17-vatry-repair.20261002-065235.bak`
Audit: `Routes.srd-cf-007-pemob-m17-vatry-repair.20261002-065235.json`

Carry-forward: re-test every AIRAC whether NATS still publishes the
DCT-compressed form and whether `PEMOB M17 VATRY` remains the valid repair.

### SRD-CF-008: RAD Annex 2A city-pair cap edits (full plan regenerated for 2610)

Count: `321` rows (`318` distinct before-rows; a few rows repeat across
multiple overlapping caps). Full compliance plan regenerated for 2610 (not
just the `EG4082` cluster originally flagged in
`airac_2610_rad_baseline.md`) using
`scripts/check_eg_city_pair_caps.py --airac 2610` (now re-pointable to any
AIRAC; was hard-coded to 2604) and
`scripts/generate_city_pair_cap_edit_spec.py`. Lowered `Max` only for
confirmed hard `H24` cap violations (`VIOLATION` verdict: all scope and
condition-tree checks confirmed met, no time-limit, no uncertain
airspace/capability condition).

Excluded from automatic edits (left unchanged, same exclusion categories as
the 2605 ledger entry):

- time-limited possible exceptions (`POSSIBLE_EXCEPTION`)
- sector/airspace-uncertain rows (`UNCERTAIN_AIRSPACE`)
- capability-conditioned rows (`UNCERTAIN_CAPABILITY`)
- combined other-verdict total: `322` rows left untouched

`EG4082` (the originally-flagged 12-row cluster, London/Farnborough group
FL095 cap) is included in this 321-row set, not treated separately.

Backup: `Routes.csv.pre-srd-cf-008-annex2a-city-pair-cap.20261002-065342.bak`
Audit: `Routes.srd-cf-008-annex2a-city-pair-cap.20261002-065342.json`
Plan: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2610\tmp\edit_spec_annex2a_cap.json`

Carry-forward: regenerate the plan fresh for each AIRAC; apply only hard
`H24` violations where route and dep/arr scope are confirmed; leave
time-limited, airspace-uncertain, and capability-conditioned findings
unchanged unless a separate supported contract exists.

### Row count summary

| Step | Rows before | Rows after | Delta |
|---|---:|---:|---:|
| Start of this pass (post SRD-CF-001) | 32465 | - | - |
| SRD-CF-002 confirmed RAD-denial family | 32465 | 32390 | -75 |
| SRD-CF-003 EG2983 Q63-entry | 32390 | 32344 | -46 |
| SRD-CF-004 EG2269 NIRIF/EVTOL | 32344 | 32283 | -61 |
| SRD-CF-005 illegal ATS BATLI/Y250/OBOXA | 32283 | 32271 | -12 |
| SRD-CF-006 MEDOG DCT VATRY | 32271 | 32134 | -137 |
| SRD-CF-007 PEMOB M17 VATRY repair | 32134 | 32134 | 0 (edit only) |
| SRD-CF-008 Annex 2A city-pair cap | 32134 | 32134 | 0 (edit only) |
| **Total** | **32465** | **32134** | **-331** |

> `out.json` regeneration and `bulk_evaluate_srd.py` re-run for this pass:
> see "Post-pass verification" below -- unexpected denials dropped
> `224` -> `11`. The carry-forward checklist for this pass is consolidated
> at the end of this file under the final "Not yet done" heading.

### SRD-CF-009: VFP-60 / EG2444A removals (re-applied)

Count: `3` rows -- identical count to the 2605 ledger entry. `EGGW`/`EGWU`
traffic via `MID Y803 SFD` toward `M605`/`UM605`, denied by `EG2444A`.
Discovered as a residual in the post-pass verification run (not caught in the
initial class sweep -- the 2605 ledger's "Route Withdrawals" section lists
this separately from the "Confirmed RAD-denial packet" section, and it was
missed on first pass through the ledger). Same family, re-applied unchanged.

Backup: `Routes.csv.pre-srd-cf-009-vfp60-eg2444-removal.20261002-070032.bak`
Audit: `Routes.srd-cf-009-vfp60-eg2444-removal.20261002-070032.json`

Carry-forward: re-check `EG2444A` every AIRAC before removing; keep as a
NATS/SRD source follow-up, not an `in.json` note-code change.

### Post-pass verification (out.json regeneration + bulk re-evaluation)

Final row count: `32465` -> `32131` (`-334` net, across SRD-CF-002
through SRD-CF-009; SRD-CF-007/008 are edits with no row-count change).

Working-copy sync: production `Routes.csv` copied to
`C:\Users\jkino\Desktop\SRD Testing Files\Routes.csv` (old working copy
backed up as
`Routes.csv.pre-srd-cf-002-008-full-recuration-refresh.<timestamp>.bak`);
`Notes.csv`/`in.json` already matched production byte-for-byte, no sync
needed.

Parser run: `dotnet run --project NewSRDParser/NewSRDParser.csproj -c
Release -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2610.1` (two runs -- first before
SRD-CF-009 was discovered, second after). Final run: `12,992` constraints
built (vs `13,171` pre-curation), `4396/4396` MC rows resolved (100%),
zero `SEG_NOT_FOUND`. Pre-existing unrelated warnings carried over
unchanged: `SctParser: Fix 'WTN' appears 2 times`, in.json linter
`Notes.csv` header-regex failure (known airac-data-fetcher PR #27 issue),
`FixResolver: Missing fix 'KEFTE<FRA>'` (pre-existing glued-token SRD data
quirk on one row, not introduced by this pass).

Bulk re-evaluation (`scripts/bulk_evaluate_srd.py --airac 2610`) against the
regenerated candidate:

| Metric | Pre-curation baseline | Post-curation (final) |
|---|---:|---:|
| Routes evaluated | 15,688 | 15,401 |
| Pass | 14,827 (94.5%) | 14,753 (95.8%) |
| Unexpected denial | **224** | **11** |
| Pass rate (raw) | 95.1% | 96.4% |
| Overall PASS | NO | NO |

Remaining 11 unexpected denials (not addressed this pass, carried forward):

- `EG3619` (2), `EG2721` (2): already-documented pre-existing residual
  noise (2606 Notes 484/485 EOBT-check family) -- not new.
- `EG3474` (2), `EG2838` (1), `EGLF1034` (1), `EG3502` (1),
  `EG5512` (1), `EG2328` (1): genuinely new, filed as Linear issues
  `VFP-440` through `VFP-445` (project `vFPC`, commitment `3. later`,
  label `hub`/`Bug`) rather than chased in this session.

Production candidate written to
`C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2610\out.json`
(cycle `2610.1`), SHA-256 `11D47FC21668AFFA451ACD32FF43E132CC838365F6F088D69CE6C5F0BCC66998`.
**Not yet uploaded to the live API** -- upload requires a separate manual
step (admin panel credentials) per `Documentation/out_json_release_runbook.md`
and was not performed this session.

Diagnostic copies for reproducibility:

- `data/local/2610/routes/out.2610_precuration.json` -- pre-curation
  candidate (cycle `2610`, 13,171 constraints), preserved for comparison.
- `data/local/2610/routes/out.2610.1_curated_final.json` -- same bytes as
  the production candidate above.
- `data/local/2610/routes/out.json` -- active Hub diagnostic input (same
  bytes as the curated final candidate).
- Traces: `data/local/2610/tmp/full_trace_2610.1_curated.jsonl` (before
  SRD-CF-009), `data/local/2610/tmp/full_trace_2610.1_final.jsonl` (after).

### Diagnostic note: Notes.csv bloat (benign, 2026-10-02)

While generating the reproducibility manifest, `Notes.csv` for this cycle
was found to be `31.6 MB` on disk versus `42-49 KB` for AIRAC 2603-2609
(a ~650x jump). Investigated before proceeding per the "ask when stuck"
workflow rule, since a size anomaly on a human-edited production source
file is exactly the kind of thing that should not be waved through.

Root cause confirmed: every row has been padded out to `16,383` columns
(one short of Excel's absolute column limit of `16,384`/`XFD`) with
empty trailing fields, while the real note content remains entirely in
column 0, identical in structure to prior cycles. This is the classic
Excel "phantom used range" artifact (scrolling/clicking/pasting near the
far-right edge of the sheet before a CSV save) -- **not data corruption,
and not an in.json/out.json integrity risk**. Logical row count is sane
(`1,797` -> `1,801` rows, consistent with normal cycle-over-cycle note
growth), and `New-SRDParser` ingests it with zero errors (CSV readers
ignore trailing empty fields), which is corroborated by the clean
`4396/4396` MC resolution and zero `SEG_NOT_FOUND` in both 2610.1 parser
runs this session.

No action taken on the file itself (owned by Ari, human-edits-only per the
file authority table). Flagged as a non-blocking cleanup item: next time
this file is opened in Excel, select all unused columns past the real data
and delete them before saving, to avoid the on-disk bloat (slow
diffs/commits, slow editor opens) going forward.

### Reproducibility verification (replay-from-backups, 2026-10-02)

Before any production upload, replayed the entire curation sequence from
scratch in an isolated working directory
(`C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2610\replay\`) to
confirm nothing was lost or drifted during this session's live,
iterative debugging (this was the first-ever full run of the 2610
pipeline, built up incrementally across two sessions).

**Routes.csv replay:** started from the pristine, untouched NATS export
(`Routes.csv.pre-srd-cf-001-jugse-spelling-fix.20261001-192434.bak`),
re-applied the SRD-CF-001 `JUSGE`->`JUGSE` fix (verified exactly 5
occurrences replaced, matching the documented table), then re-ran
SRD-CF-002 through SRD-CF-009 via the real saved spec JSON files
(`data/local/2610/tmp/match_*.json`, `removal_spec_*.json`,
`edit_spec_*.json`) and the actual scripts
(`apply_routes_csv_removal.py`, `apply_routes_csv_edit.py`), in
documented order. Every single step reproduced the exact documented row
count with zero "requested but not found" refusals:
`32465->32390->32344->32283->32271->32134->32134->32134->32131`.
Final replayed `Routes.csv` SHA-256
`8BA8B15713DDCB9300BB507917A9E9D1CE3208076759B4DFAA3A5C17D2A87B99` -
**byte-identical** to the production file at the top of this document.

**Parser replay:** confirmed the parser working copy
(`C:\Users\jkino\Desktop\SRD Testing Files\{Routes,Notes}.csv`,
`in.json`) still hash-matched production exactly, then re-ran
`New-SRDParser` (`dotnet run ... CYCLE_OVERRIDE=2610.1`) fresh. Output:
`12,992` constraints, `4396/4396` MC rows resolved (100%), identical
pre-existing warnings (`WTN` duplicate, `KEFTE<FRA>` missing fix,
`Notes.csv` linter header-regex gap). Resulting `out.json` content is
byte-identical to the promoted production candidate after excluding the
`generated.generatedAtUtc` timestamp field (same `gitSha` `f9d7d03`, same
`gitRef`).

**Evaluator replay:** re-ran `bulk_evaluate_srd.py --airac 2610` fresh -
identical results: `15,401` routes evaluated, `14,753` pass (95.8%),
`11` unexpected denial, `96.4%` raw pass rate, `99.9%` closeout
non-failure. Exact match with the post-pass verification table above.

**Conclusion:** the documented curation sequence is fully and precisely
reproducible end-to-end from the pristine NATS export through the final
promoted `out.json` candidate. No hidden state, no manual-edit drift, no
information lost during this session's live problem-solving. Production
upload may proceed with confidence once `VFP-446` (`EGD036`
confirmation) is resolved.

### Not yet done (still carried forward)

Filed as real Linear issues (project `vFPC`) rather than left as prose this
time, per the issue-hygiene workflow:

- `VFP-440` -- `EG3474` unexpected denial, `EGVA`/`EGVN` CONKO...LARGA
- `VFP-441` -- `EG2838` unexpected denial, `EGAE` -> `EGTE`
- `VFP-442` -- `EGLF1034` unexpected denial, `EGJJ` -> `EGKK`
- `VFP-443` -- `EG3502` unexpected denial, `EGLL` -> `EGNX`
- `VFP-444` -- `EG5512` unexpected denial, `EGSS` -> `EGSH`
- `VFP-445` -- `EG2328` unexpected denial, `EGTC` departure
- `VFP-446` -- confirm `EGD036` restricted-area active-list entry before
  release sign-off (commitment `2. next`, since it should ideally resolve
  before `out.json` cycle `2610.1` goes live)

Still just prose (not filed; genuinely lower-urgency/external/recurring):

- Upload `out.json` (cycle `2610.1`) to the live API after a final human
  sign-off, per `Documentation/out_json_release_runbook.md` (manual
  release step, not a backlog item).
- Roll RAD-Parser integration test constants (`tests/conftest.py`).
- Refresh NATS AIXM sector XML (manual portal fetch, stale since 2604).
- RSA-at-root systemic audit (Linear `VFP-439`, pre-existing): 53 other
  rules still need triage.
- Run `scripts/airac_repro_bundle.py --airac 2610` and `airac-archiver`
  once the release is finalized (not done this session -- this is a
  diagnostic/curation session, not the final release sign-off).
