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


## Note 270 route-scoped EGJ* ORIST coding test

Date: 2026-05-28

Purpose: replace the broad EGJ* Note 270 hard-ban branches with route-scoped variants now that New-SRDParser supports `route_scope` note variants. This avoids blocking EGJ* prop/turboprop traffic on the ORIST/L982 family while preserving the hard ban for the ORIST/Y110 family.

Method: kept the non-EGJ*/non-EGKK broad Note 270 ban. Replaced the EGJ* branches with:

- `egj-orist-l982-jet-electric-ban`: `dep=["EGJ"]`, `nodests=["EGKK"]`, `route_scope.sid="ORIST"`, `route_scope.airways=["L982"]`, `types=["J","E"]`.
- `egj-orist-y110-ban`: `dep=["EGJ"]`, `nodests=["EGKK"]`, `route_scope.sid="ORIST"`, `route_scope.airways=["Y110"]`, `types=["J","E","P","T"]`.

Verification: generated a scratch parser run with `CYCLE_OVERRIDE=2605.7`. EGJJ/ORIST output had 60 Note 270 alert constraints: 31 L982-only constraints with `types=["J","E"]`, 29 Y110-only constraints with `types=["J","E","P","T"]`, 31 matching L982 P/T pass-through constraints, and 0 mixed L982+Y110 Note 270 route alternatives. `vfpc_check` probes against the generated output confirmed L982/J fails at the alert round, L982/P passes, and Y110/P fails at the alert round.

Operational confirmations: the earlier broad P/T `min=195` ban branch was intentionally removed for the route-scoped model; EGJ* prop/turboprop traffic on ORIST/L982 should pass rather than be blocked above FL195 by Note 270. AIRAC 2605 `Routes.csv` has no EGJ*/ORIST first-airway family other than L982 or Y110; all EGJ*/ORIST rows carrying Note 270 are in those two families.
