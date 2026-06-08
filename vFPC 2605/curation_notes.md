# AIRAC 2605 manual curation note

Date: 2026-05-14

Purpose: remove illegal SRD route rows from the live AIRAC 2605 input before rerunning New-SRDParser, while preserving an audit trail of what was removed and why.

Reason: the following route fragments are not present in the current AIRAC and make the affected SRD rows illegal:

- `UNTAL N110 DOLAS`
- `UNTAL N110 ERKIT`
- `TIPTA UP17 POL`
- `POL Y250 OBOXA`

These are the same defects reported in AIRAC 2604 (see `vFPC 2604/curation_notes.md`). NATS has not corrected them and re-published the identical illegal routes in 2605.

Removed rows from `Routes.csv`:

- Worksheet line 11798 (`EGNCLAMSO002`)
Route: `DCT UNTAL N110 DOLAS L603`
Remarks: none
Defect: Segment UNTAL N110 DOLAS does not exist in the current AIRAC.
Original CSV row: `EGNC,,245,660,DCT UNTAL N110 DOLAS L603,,LAMSO,`

- Worksheet line 11806 (`EGNCNAVPI002`)
Route: `DCT UNTAL N110 DOLAS DCT`
Remarks: none
Defect: Segment UNTAL N110 DOLAS does not exist in the current AIRAC.
Original CSV row: `EGNC,,265,660,DCT UNTAL N110 DOLAS DCT,,NAVPI,`

- Worksheet line 11807 (`EGNCNAVPI003`)
Route: `DCT UNTAL N110 ERKIT L602 OTR L90 DOLAS DCT`
Remarks: `Notes: 467 - 523`
Defect: Segment UNTAL N110 ERKIT does not exist in the current AIRAC.
Original CSV row: `EGNC,,265,660,DCT UNTAL N110 ERKIT L602 OTR L90 DOLAS DCT,,NAVPI,Notes: 467 - 523 `

- Worksheet line 21238 (`EGXWGOREV001`)
Route: `DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> SURAT DCT`
Remarks: none
Defect: Segments TIPTA UP17 POL and POL Y250 OBOXA do not exist in the current AIRAC.
Original CSV row: `EGXW,,255,660,DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> SURAT DCT,,GOREV,`

- Worksheet line 21256 (`EGXWNIVUN001`)
Route: `DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> LAMRO DCT`
Remarks: `Notes: 396`
Defect: Segments TIPTA UP17 POL and POL Y250 OBOXA do not exist in the current AIRAC.
Original CSV row: `EGXW,,255,660,DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> LAMRO DCT,,NIVUN,Notes: 396`

- Worksheet line 21260 (`EGXWPETIL001`)
Route: `DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> SURAT DCT`
Remarks: none
Defect: Segments TIPTA UP17 POL and POL Y250 OBOXA do not exist in the current AIRAC.
Original CSV row: `EGXW,,255,660,DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> SURAT DCT,,PETIL,`

- Worksheet line 21288 (`EGXWTINAC001`)
Route: `DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> ITSUX DCT`
Remarks: none
Defect: Segments TIPTA UP17 POL and POL Y250 OBOXA do not exist in the current AIRAC.
Original CSV row: `EGXW,,255,660,DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> ITSUX DCT,,TINAC,`

- Worksheet line 21289 (`EGXWVAXIT001`)
Route: `DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> REKNA DCT`
Remarks: none
Defect: Segments TIPTA UP17 POL and POL Y250 OBOXA do not exist in the current AIRAC.
Original CSV row: `EGXW,,255,660,DCT TIPTA GAT UP17 POL Y250 OBOXA P17 MEJAC <FRA> REKNA DCT,,VAXIT,`

## Summer-time current-season coding

Date: 2026-05-16

Purpose: generate the AIRAC 2605.3 `out.json` during UK summer time using the explicit summer variants in `Notes.csv`, without changing parser code.

Method: checked `Notes.csv` for notes that explicitly state a summer-time variant, then updated the corresponding timed `in.json` entries only where a coded time window already existed. Most affected notes move the coded UTC window one hour earlier. Explicit bracketed summer windows were followed where present, notably Note 335 ELVOS (`2330-0500`) and Note 516 allowed window (`1700-0800 Summer UTC`, encoded as a hard-ban complement `0801-1659`).

Affected coded notes: 258, 295, 335, 338, 339, 344, 350, 374, 382, 447, 455, 468, 469, 470, 484, 485, 516.

Follow-up: New-SRDParser#170 tracks automating summer/winter SRD time conversion so future cycles do not need this manual current-season data pass.

## Note 516 route-note curation

Date: 2026-05-16

Purpose: remove one apparent wrong Note 516 route-note assignment while preserving the source audit trail for the next AIRAC.

Source note: `VAMEB/KEFTE - MAMUL - MOGLI - ODVOD route`; available only `1800-0900 (1700-0800 Summer) UTC`.

Audit rule used: rows carrying Note 516 should contain `VAMEB` or `KEFTE` plus `MAMUL`, `MOGLI`, and `ODVOD`. Re-check next AIRAC by searching for:

- rows with Note 516 that do not match `VAMEB|KEFTE` + `MAMUL` + `MOGLI` + `ODVOD`
- rows that do match that route pattern but do not carry Note 516

Result on the curated AIRAC 2605 `Routes.csv` before this edit:

- 49 rows carried Note 516.
- 48 rows matched the route pattern.
- 0 matching route-pattern rows were missing Note 516.
- 1 row carried Note 516 but did not match the route pattern.

Edited row:

- Data row 308 / worksheet line 309 (`AGORI` to `EGKB`, `LISTO1C`, FL345-660)
Route: `DCT OSBOX DCT IBROD <FRA> AKOMU DCT VAMEB UL612 LISTO`
Original remarks: `Notes: 516`
New remarks: blank
Reason: route goes `VAMEB UL612 LISTO`; it does not use the Note 516 route `VAMEB/KEFTE - MAMUL - MOGLI - ODVOD`.

Follow-up: Linear `VFP-138` tracks asking NATS to review the source placement if the same assignment reappears.

## AIRAC 2605 point-release publication status

Date: 2026-05-16

Purpose: make the local/archive point-release history explicit before production publication cleanup.

- `out.2605.1.json`: initial AIRAC 2605 output; documented as uploaded/published in Butler notes.
- `out.2605.2.json`: archived/generated intermediate; not believed to have been uploaded/published.
- `out.2605.3.json`: archived/generated summer-time intermediate; not believed to have been uploaded/published.
- `out.2605.4.json`: final curated/audited candidate generated after Note 516 route-note curation; intended as the replacement upload candidate.

Current local production-folder `out.json` now has cycle `2605.4`, 90 airports, 13,861 constraints, SHA-256 `1DFA000E8EBEEAEEA3956F0D853A5BFBA1B293AEB3C371AFC85A7E066DB0DD8B`.

Archive cleanup for `.2`/`.3` should wait until `.4` is uploaded and stable.

- 2026-05-16: Removed SRD Note 270 from Routes.csv line 8210 (EGJJ ORIST L982 VASUX to EGKK). Note 270 explicitly excepts ARR EGKK, but parser output still applied hard-ban branches to the EGKK row; keeping Note 453 only prevents a false EGKK-arrival ban pending the longer parser/vFPC issue for broad-ban exception ordering. Backup: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv.pre-note270-egkk-curation.20260516-095947.bak

- 2026-05-16: Updated in.json Note 397 comment after final spot-check. Current AIRAC 2605 rows carrying Note 397 are UTFAV/min-FL345 rows, not the earlier suspected RINTI rows; no runtime note coding added. Backup: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\in.json.pre-note397-comment.20260516-100207.bak

- 2026-05-16: Final AIRAC 2605.4 rerun after Note 270 EGKK route curation and Note 397 comment correction. Output copied to production candidate out.json. Final counts: 90 airports, 13,859 constraints, SHA256 6F0A2C1D8256E46758D283303F7AB0A72567DE7BFD1F636C1C70EA63C73ECF5A. Note 270 runtime alerts: 135 total, 0 with destination EGKK.


## RAD Annex 2A city-pair cap curation

Date: 2026-05-22

Purpose: lower SRD `Routes.csv` row max levels where the row exceeded a confirmed H24 RAD Annex 2A city-pair cap. This keeps the generated route constraints aligned with RAD profile caps rather than allowing the SRD row's broader max level to publish above the RAD cap.

Source data: `data/local/2605/rad/annex2a_runtime_rules.json` and `data/local/2605/rad/annex1_groups.json`, generated from `RAD_2605_v1_16.xlsx`.

Method: ran the city-pair cap compliance check against this AIRAC 2605 `Routes.csv`. Edited only findings classified as `VIOLATION`: dep/arr scope matched, route conditions were confirmed, cap was H24, and the SRD row `Max` exceeded the RAD cap. Left unchanged: 69 time-limited possible exceptions, 243 sector/airspace-uncertain rows, and 17 capability-conditioned rows.

Result: lowered `Max` on 336 route rows across 154 dep/arr pairs. Rule hit counts: EG4002=11, EG4009=1, EG4017=23, EG4018=2, EG4025=1, EG4063=61, EG4070=8, EG4077=1, EG4082=29, EG4092=77, EG4103=2, EG4107=1, EG4111=2, EG4119=4, EG4134=4, EG4137=2, EG4139=22, EG4141=1, EG4145=4, EG4152=9, EG4161=1, EG4162=4, EG4163=6, EG4164=1, EG4175=9, EG4178=3, EG4185=1, EG4191=11, EG4196=35.

Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv.pre-city-pair-cap-curation.20260522-082432.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.city_pair_cap_edits.20260522-082432.json`


## Note 339 route-note curation

Date: 2026-05-22

Purpose: remove Note 339 from rows that do not match the route family described by the note text. Note 339 is specific to `VASUX DCT ELVOS` / `VASUX DCT LESTA` availability at RFL285+; the edited rows use the `ORIST/Y110 VEXEN L980 KATHY/AVANT` family and contain neither `VASUX` nor `ELVOS`/`LESTA`.

Method: searched all `Routes.csv` rows carrying Note 339. Kept all rows containing `VASUX` and either `ELVOS` or `LESTA`. Removed only the `339` token from the four non-matching rows, preserving other notes.

Edited rows: 8213, 8216, 8219, 26526.

Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv.pre-note339-curation.20260522-084127.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.note339_curation.20260522-084127.json`


## Note 492 source sync for publication run

Date: 2026-05-22

Purpose: carry forward the already-merged AIRAC 2605.5 Note 492 fix before generating the next production candidate. The desktop source `in.json` still had the older scoped Note 492 hard-rule shape from 2605.4; `airac-data` commit `c931a43` changed Note 492 to comment-only after IFPUV accepted the affected EGKK rows.

Method: copied `C:\Users\jkino\Documents\GitHub\airac-data\vFPC 2605\in.json` to this source folder before the parser run.

Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\in.json.pre-note492-sync.20260522-084323.bak`


## AIRAC 2605.6 production candidate generation

Date: 2026-05-22

Purpose: generate a new publication candidate after carrying forward the Note 492 2605.5 fix, lowering confirmed H24 RAD Annex 2A city-pair caps in `Routes.csv`, and removing misplaced Note 339 from non-`VASUX DCT ELVOS/LESTA` rows.

Parser: New-SRDParser `main`, git SHA `7b0948c`.
Cycle override: `2605.6`.
Generated candidate: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\out.json`.
Previous candidate backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\out.json.pre-2605.6-publication-candidate.20260522-084659.bak`.
Final counts: 90 airports, 13822 constraints, SHA256 `DFFA04F74C90A51C988769303B003532A7261152410BED9B8D809D9D09D70221`.
Verification highlights: 0 `srd:492` alerts, 0 Note 270 alerts with destination EGKK, 0 unresolved AIP segments.

## AIRAC 2605.7 Peter manual-test candidate

Date: 2026-05-28

Purpose: generate a local production-style candidate for Peter's manual testing only. This candidate has not been uploaded to the live API.

Carry-forward gate: reviewed the active SRD carry-forward ledger before generation. The production folder still had the older Note 270 hard-rule shape, so `in.json` was synced from the accepted `airac-data\vFPC 2605\in.json` baseline containing the route-scoped VFP-229 Note 270 variants.

Backups:
- Previous `out.json`: `out.json.pre-2605.7-peter-test.20260528.bak`
- Previous `in.json`: `in.json.pre-note270-route-scope-2605.7.20260528.bak`

Parser: New-SRDParser `main`, git SHA `b80df7c`.
Cycle override: `2605.7`.
Generated candidate: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\out.json`.
Final counts: 90 airports, 13780 constraints, SHA256 `C27A50B034C5E81472E0245C24146DC59EA9615B66C37444E82F74E3510498AB`.
Sidecars: `run_summary.2605.7.json`, `seg_not_found_summary.2605.7.json`.
MC resolution: 4626/4626 resolved; 0 unresolved AIP segments.

Validation:
- `in.json` JSON parse: pass.
- `python Tools\lint_in_json.py --airac-dir <vFPC 2605>`: 0 errors, 0 warnings.
- `dotnet test Testing\Testing.csproj --no-restore --filter "FullyQualifiedName!~RouteSelectionGeneratorTests"`: 1074 passed, 15 skipped.
- Full New-SRDParser test run without filter hits the expected oracle-regeneration guard in `RouteSelectionGeneratorTests` and was not used for this dry production candidate.
- UKVFPCAPI generated-output mock upload with `VFPC_TEST_OUT_JSON=<vFPC 2605\out.json>`: pass.
- `go test ./...` in UKVFPCAPI: pass.
- VFPC-next validator/core smoke tests: pass.
- VFPC legacy unit/time-window tests: pass. Legacy `RuntimeConstraintTest.*` binary could not load the current envelope output in this environment; no rebuild toolchain was available locally.

Note 270 output check:
- 31 EGJJ/ORIST L982 Note 270 J/E ban constraints.
- 31 EGJJ/ORIST L982 P/T pass-through constraints with no Note 270 alert.
- 29 EGJJ/ORIST Y110 Note 270 J/E/P/T ban constraints.
- 0 mixed L982/Y110 Note 270 alert constraints.

VFPC_Check probes against this out.json:
- EGJJ-EGSS ORIST L982 TELTU, B738/J, FL190: FAIL at alerts (expected L982 J/E Note 270 ban).
- EGJJ-EGSS ORIST L982 TELTU, PA34/P, FL190: PASS (expected L982 P/T pass-through).
- EGJJ-EGBB ORIST Y110 VEXEN L980 AVANT M184 HEMEL, PA34/P, FL230: FAIL at alerts (expected Y110 all-type Note 270 ban).

## AIRAC 2605.8 VFP-60 EG2444 route curation

Date: 2026-05-29

Purpose: remove SRD route rows that current RAD/IFPUV evidence rejects under EG2444A before continuing expanded SRD/RAD testing.

Decision: treat VFP-60 as a local route curation plus NATS follow-up, not an in.json note-coding change. The conflicting rows are ordinary SRD route candidates; leaving them in Routes.csv causes New-SRDParser to publish them as available constraints. Current EG2444A has NOT AVBL FOR TFC for traffic via MID Y803 SFD with branch 2 applying to DEP EGGW/EGLL/EGWU. IFPUV evidence for representative EGGW probes rejects by EG2444A. The EGWU row is inferred from the same explicit EG2444A branch and remains part of the NATS clarification.

Removed rows: worksheet lines 7198, 7200, and 21193.

Backups:
- Production Routes.csv: $prodRoutesBackup
- SRD Testing Files Routes.csv: $testRoutesBackup
- Previous curation_notes.md: $prodNotesBackup
- Previous out.json: $prodOutBackup

Audit detail:
- Production row-removal note: $prodAudit
- Working-copy row-removal note: $testAudit
- Hub curation spec: data/local/2605/vfp60_eg2444_route_curation.json

Parser: New-SRDParser main via dotnet run --project NewSRDParser/NewSRDParser.csproj -c Release -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2605.8.
Generated candidate: $prodOut.
Final counts: 90 airports, 13810 constraints, SHA256 $sha.
Sidecars: 
un_summary.2605.8.json, seg_not_found_summary.2605.8.json.


## AIRAC 2605.9 confirmed RAD-denial route curation

Date: 2026-06-08

Purpose: remove SRD route rows that the June 5 confirmed RAD-denial correction packet classified as `correct_rad_denial_not_encoded_in_srd` before generating the next production dot-release candidate.

Contract layer: source data / tracked fact. These are route-set/source disagreements, not parser or evaluator compatibility guards.

Source packet: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\Documentation\butler\staging\nats_srd_confirmed_rad_denials_2605_2026-06-05.csv`.

Decision: removed exact current-cycle `Routes.csv` rows matched by departure, SID, destination/exit, normalized route text, and FL band. Diagnostic route indexes are not written into production source; they are retained in the audit detail.

Removed source rows: 25. The packet has 22 flattened route rows; current `Routes.csv` maps those to 25 physical CSV rows because one packet row covers two destinations and two EGTE/EG3217 source routes were duplicated.

Rule/source row counts:
- `EG2013`: 2
- `EG2522`: 2
- `EG2608, EG2628`: 1
- `EG2640`: 1
- `EG2721`: 2
- `EG3217`: 12
- `EG3499`: 3
- `EG3619`: 2

Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv.pre-confirmed-rad-denial-curation.20260608-071406.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.confirmed_rad_denial_curation.20260608-071406.json`
Previous curation note backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\curation_notes.md.pre-confirmed-rad-denial-curation.20260608-071406.bak`

Deferred/not patched: `VFP-320` / `VFP-338` EG5594/EGPK procedure-context rows remain data-blocked on real AIP SID/STAR procedure facts and were not edited in this source run.

## AIRAC 2605.9 production candidate generation

Date: 2026-06-08

Purpose: generate a production dot-release candidate after removing the June 5 confirmed RAD-denial route-set correction packet rows from Routes.csv.

Pre-run gate:
- Source in.json (SOURCE OF TRUTH): C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\in.json
- Working-copy in.json (LEGACY WORKING COPY): C:\Users\jkino\Desktop\SRD Testing Files\in.json
- Working copy refreshed from source immediately before parser run; hashes for Routes.csv, in.json, and Notes.csv matched source.
- Generated output (LEGACY GENERATED): C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json
- Promoted candidate (PROMOTED PRODUCTION CANDIDATE): C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\out.json

Parser: New-SRDParser main, git SHA $parserSha.
Command: dotnet run --project NewSRDParser\NewSRDParser.csproj -c Release --no-restore -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2605.9.
Cycle override: 2605.9.
Final counts: 90 airports, 14678 constraints, SHA256 $sha.
MC resolution: 4618/4618 resolved; unresolved rows 0; seg-not-found rows 0.
Sidecars: 
un_summary.2605.9.json, seg_not_found_summary.2605.9.json.
Previous candidate backup: out.json.pre-2605.9-confirmed-rad-denial-curation.20260608-071645.bak.

Parser log note: the run still logs the known duplicate SCT fix WTN; output was written successfully and MC resolution was complete.


## AIRAC 2605.9 EG2721 SAM source curation follow-up

Date: 2026-06-08
Contract layer: source data / tracked fact.

A post-generation signature check against Documentation/butler/staging/nats_srd_confirmed_rad_denials_2605_2026-06-05.csv found two remaining generated constraints matching the confirmed EG2721 RAD-denial packet after the first curation pass.  The missed source rows used SID GOGSI but normalize to the same EGLL -> EGHI/EGHH SAM/BIA route signatures at FL065-075.

Removed source rows from Routes.csv:

- EGLL GOGSI FL065-075 DCT SAM DCT BIA -> EGHH
- EGLL GOGSI FL065-075 DCT SAM DCT -> EGHI

Backup: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv.pre-eg2721-sam-curation.20260608-081120.bak
Audit: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.eg2721_sam_curation.20260608-081120.json


## AIRAC 2605.9 final production candidate generation after EG2721 follow-up

Date: 2026-06-08
Contract layer: source data / tracked fact, then parser/generated production artifact.

This final 2605.9 candidate supersedes the earlier same-day candidate generated before the EG2721 SAM follow-up curation.

Pre-run gate roles:

- Source in.json: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\in.json
- Source Routes.csv: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\Routes.csv
- Working parser copy: C:\Users\jkino\Desktop\SRD Testing Files
- Generated output: C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json
- Promoted production candidate: C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2605\out.json

Parser command:

```text
dotnet run --project NewSRDParser\NewSRDParser.csproj -c Release --no-restore -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2605.9
```

Parser git SHA: c6307da
Routes.csv SHA256: B6E683395E7871279B0373CD7C9790803C91F822C7C6DEF456AEE0791A342585
out.json SHA256: 1CEB785900BA1879ED0D29218BCAE445B7CDA5588DA623194D00CDC16288C481

Run summary:

- AIRAC cycle: 2605.9
- Constraints built: 14676
- MC resolved: 4618/4618
- Seg-not-found unresolved rows: 0
- Seg-not-found total rows: 0

Known parser log note: SCT parser still reports duplicate WTN fix, but the parser run completed and wrote output.


## AIRAC 2605.9 final verification evidence

Date: 2026-06-08

Targeted confirmed RAD-denial packet check:

- Packet: Documentation/butler/staging/nats_srd_confirmed_rad_denials_2605_2026-06-05.csv
- Expanded complete packet signatures checked: 18
- Generated constraints checked: 14676
- Remaining exact matches: 0

Production-facing bulk SRD/RAD evaluation:

```text
python scripts\bulk_evaluate_srd.py --airac 2605 --srd-profile rnav1-jet --trace-out data\local\2605\tmp\srd_2605_9_rnav1_jet_final_20260608.jsonl --classify-srd-reasons --show-failures 0 --show-errors 0
```

Summary:

- Total routes evaluated: 16465
- Pass: 14628
- Referred: 1215
- Missing denial: 0
- Time-conditioned missing denial: 76
- Conditional SRD not asserted: 1
- Procedure context needed: 334
- Unexpected denial: 211
- Confirmed RAD authority: 208 resolved
- Partial evidence covered: 1
- Unreviewed unexpected: 2
- Eval errors: 0
- Raw pass rate: 95.9%
- Resolved rate: 97.3%
- Overall PASS: NO, due known non-pass buckets above.

Parser tests:

- Full parser test command excluding RouteSelectionGeneratorTests failed 12 pre-production verification tests because Testing/TestData selection hashes are stale relative to the refreshed production working source files.
- Rerun excluding the stale PreProductionVerificationTests passed: 1071 passed, 15 skipped, 0 failed.
