## AIRAC 2607 SRD carry-forward curation (20260712-200442)

Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv`
Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv.pre-2607-srd-curation.20260712-200442.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.srd_carry_forward_curation.20260712-200442.json`
City-pair cap audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.city_pair_cap_edits.20260712-200442.json`

Applied source-data/tracked-fact curations:
- SRD-CF-001 illegal ATS-route withdrawals: 15 rows removed.
- SRD-CF-003 MEDOG DCT VATRY direct-route withdrawals: 137 rows removed.
- SRD-CF-004 PEMOB DCT VATRY repair: 162 rows changed to `PEMOB M17 VATRY`.
- SRD-CF-004 RAD Annex 2A H24 city-pair cap lowering: 324 row max levels lowered.
- Note 516 misplaced route-note token removal: 1 row.
- Note 339 misplaced route-note token removal: 4 rows.
- Note 480 DVR L10 RINTI FL-band repair: 41 rows set to Min 115 / Max 245.

Rows before/after: `32473` -> `32321`.

City-pair exclusions left unchanged:
- `POSSIBLE_EXCEPTION`: 69
- `UNCERTAIN_AIRSPACE`: 243
- `UNCERTAIN_CAPABILITY`: 17

Parser/linter/output verification recorded below. Hub RAD evaluator baseline remains the next phase.
## AIRAC 2607 post-curation parser verification (20260712-2008)

Structural source checks after curation:
- `in.json` JSON parse: passed.
- `Tools\lint_in_json.py --in-json ... --notes-csv ... --routes-csv ...`: 0 errors, 0 warnings.
- Carry-forward source scans: illegal UNTAL/BATLI rows 0; direct `MEDOG DCT VATRY` rows 0; full `MEDOG DCT KRAGY DCT PEMOB DCT VATRY` rows 0; misplaced Note 516 rows 0; misplaced Note 339 rows 0; Note 480 DVR L10 RINTI rows below FL115 0.
- Known parked non-KRAGY `PEMOB DCT VATRY` row remains: 1.
- Repaired `MEDOG DCT KRAGY DCT PEMOB M17 VATRY` rows after curation: 193.

RAD Annex 2A city-pair verification:
- 2607 RAD runtime input: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\rad\annex2a_runtime_rules.json`.
- Post-curation city-pair compliance: 0 hard H24 `VIOLATION` rows remain.
- Left unchanged by design: `POSSIBLE_EXCEPTION` 69, `UNCERTAIN_AIRSPACE` 243, `UNCERTAIN_CAPABILITY` 17.

Parser candidate generation:
- Working copy refreshed from this source set to `C:\Users\jkino\Desktop\SRD Testing Files` before parser run.
- Command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2607`.
- Generated candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- Candidate role: legacy generated / explicit diagnostic candidate, not promoted production candidate.
- AIRAC cycle: 2607.
- Output constraints: 14508.
- Airports: 89.
- SHA-256: `2DCD18F351234DC07C1A5E7FE1790183F94EA1784A6524E50BA580661D7898E0`.
- MC rows resolved: 4452/4452.
- Segment-not-found unresolved rows: 0.
- Candidate copied for Hub diagnostics to `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.

## AIRAC 2607 RAD static-output curation (20260712-210056)

Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv`
Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv.pre-rad-static-output-curation.20260712-210056.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.rad_static_output_curation.20260712-210056.json`

Applied RAD-backed static-output curations:
- EG3499 WEVBE above FL245 not available except EGTE arrival via ALHUP: 22 row(s), action `cap_max`.
- EG4121 caps EGHH/EGHI/EGLF-family LF DVR L10 RINTI flow at FL155: 7 row(s), action `cap_max`.
- EG2269 NIRIF requires active EGD201F/G or associated FBZ; unavailable in static vFPC output: 61 row(s), action `remove`.
- EG2640 EGJJ to EGPK via BLACA unavailable; use POL/RIBEL routing: 1 row(s), action `remove`.
- EG2983 Q63 VATRY requires danger-area/M17 availability state; unavailable in static vFPC output: 82 row(s), action `remove`.
- EG3217 CARWI above FL245 unavailable outside published exception: 22 row(s), action `remove`.
- EG3499 WEVBE above FL245 not available except EGTE arrival via ALHUP: 33 row(s), action `remove`.
- EG3619 EGPT via INREV unavailable unless routing via RIBEL or LEHOC: 5 row(s), action `remove`.
- EG4121 caps EGHH/EGHI/EGLF-family LF DVR L10 RINTI flow at FL155: 6 row(s), action `remove`.

Rows before/after: `32321` -> `32111`.

Policy note: EG2269 NIRIF and EG2983 Q63 VATRY are real-world conditional
routes requiring danger-area/M17 availability state. They are suppressed
for static vFPC output because UK VATSIM gameplay does not currently model
those danger areas as active. If route-by-route RAD evaluation later consumes
live activation facts, these should move from source suppression to dynamic
runtime evaluation.
## AIRAC 2607 post-RAD-static-output verification (20260712-2102)

Structural source checks after RAD static-output curation:
- `in.json` JSON parse: passed.
- `Tools\lint_in_json.py --in-json ... --notes-csv ... --routes-csv ...`: 0 errors, 0 warnings.
- Source predicate verification after curation: EG3499 WEVBE above FL245 0; EG3217 CARWI above FL245 0; EG4121 DVR L10 RINTI above FL155 0; EG3619 INREV EGPT without RIBEL/LEHOC 0; EG2640 EGJJ-EGPK via BLACA 0; EG2269 EIDW via NIRIF 0; EG2983 EIDW via Q63 VATRY 0.

Parser candidate generation after RAD static-output curation:
- Working copy refreshed from this source set to `C:\Users\jkino\Desktop\SRD Testing Files` before parser run.
- Command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2607`.
- Generated candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- AIRAC cycle: 2607.
- Output constraints: 14324.
- MC rows resolved: 4403/4403.
- Segment-not-found unresolved rows: 0.
- Candidate copied for Hub diagnostics to `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.

RAD evaluator verification after RAD static-output curation:
- Command: `python scripts\bulk_evaluate_srd.py --airac 2607 --classify-srd-reasons --trace-out data\local\2607\tmp\full_trace_2607_after_rad_static_output_curation.jsonl --summary-json data\local\2607\tmp\full_trace_2607_after_rad_static_output_curation.summary.json --show-failures 0 --show-errors 0` with `PYTHONPATH=C:\Users\jkino\Documents\GitHub\RAD-Parser\src`.
- Routes evaluated: 16020.
- Pass: 14937.
- Referred: 205.
- Missing denial: 0.
- Time-conditioned missing denial: 81.
- SRD reason represented: 5.
- Conditional SRD not asserted: 776.
- Unexpected denial: 16.
- Eval errors: 0.
- Previous no-evidence unexpected-denial count before this curation was 205, so the RAD static-output curation removed 189 unexpected-denial rows from the generated population.

## 20260712-211449 RAD residual static-output curation

Applied after the AIRAC 2607 RAD bulk evaluator left 16 unexpected static SRD denials. Edits were limited to confirmed source-data defects that are expressible in the SRD framework.

- Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv` (SOURCE OF TRUTH)
- Backup: `Routes.csv.pre-rad-residual-curation.20260712-211449.bak`
- Audit: `Routes.rad_residual_curation.20260712-211449.json`
- Removed rows: 13
- Capped rows: 6
- Removed confirmed RAD-authority rows for EG2608/EG2628, EG2444, EG2721, EG2328, and EG3474/EG5793.
- Capped EG2651 Midlands DET L6 DVR rows to FL155 and EG4171 EGTK/Lille-group rows to FL195.
- Left EG2838 and EG3502 unmutated: EG2838 remains different-authority/reviewed-investigation; EG3502 remains SID/STAR modelling debt.

Parser candidate generation after RAD residual curation:
- Working copy refreshed from this source set to `C:\Users\jkino\Desktop\SRD Testing Files` before parser run.
- Command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2607`.
- Generated candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- AIRAC cycle: 2607.
- Output constraints: 14317.
- MC rows resolved: 4400/4400.
- Segment-not-found unresolved rows: 0.
- Candidate copied for Hub diagnostics to `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.
- Generated/Hub SHA256: `C089487F17E2CE5AF4E07FE2F91AEE0C36E20F782B35A34045676EE3DC5CEE45`.

RAD evaluator verification after RAD residual curation:
- Command: `python scripts\bulk_evaluate_srd.py --airac 2607 --classify-srd-reasons --trace-out data\local\2607\tmp\full_trace_2607_after_rad_residual_curation.jsonl --summary-json data\local\2607\tmp\full_trace_2607_after_rad_residual_curation.summary.json --show-failures 0 --show-errors 0` with `PYTHONPATH=C:\Users\jkino\Documents\GitHub\RAD-Parser\src`.
- Routes evaluated: 16011.
- Pass: 14942.
- Referred: 205.
- Missing denial: 0.
- Time-conditioned missing denial: 81.
- SRD reason represented: 5.
- Conditional SRD not asserted: 776.
- Unexpected denial: 2.
- Eval errors: 0.
- Remaining unexpected denials are the held non-source cases: EG2838 EGAE-EGTE Q38/SOSIM and EG3502 EGLL ULTIB-EGNX T420 WELIN.

## 20260712-213851 final IFPUV-backed RAD residual curation

Applied after targeted IFPUV probes resolved the final two AIRAC 2607 unexpected RAD denials.

- Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\Routes.csv` (SOURCE OF TRUTH)
- Backup: `Routes.csv.pre-final-ifpuv-curation.20260712-213851.bak`
- Audit: `Routes.final_ifpuv_curation.20260712-213851.json`
- EG2838: capped EGAE -> EGTE `MAKUX DCT SOSIM Q38 MALUD` row from Max 335 to Max 275. IFPUV P2838A cited EG2838C and F285..F999 non-existence; P2838B below FL285 and P2838C alternate high-level route had no errors.
- EG3502: removed EGLL ULTIB/HEMEL1E -> EGNX `T420 WELIN` row. IFPUV P3502A cited EG3502A for HEMEL T420 WELIN.

Parser candidate generation after final IFPUV-backed curation:
- Working copy refreshed from this source set to `C:\Users\jkino\Desktop\SRD Testing Files` before parser run.
- Command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2607`.
- Generated candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- AIRAC cycle: 2607.
- Output constraints: 14316.
- MC rows resolved: 4399/4399.
- Segment-not-found unresolved rows: 0.
- Candidate copied for Hub diagnostics to `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.
- Generated/Hub SHA256: `CBCD9A841986988D1E2C580F9DC8B2F8819619B3672768381C95D300A1D49CEE`.

RAD evaluator verification after final IFPUV-backed curation:
- Command: `python scripts\bulk_evaluate_srd.py --airac 2607 --classify-srd-reasons --trace-out data\local\2607\tmp\full_trace_2607_after_final_ifpuv_curation.jsonl --summary-json data\local\2607\tmp\full_trace_2607_after_final_ifpuv_curation.summary.json --show-failures 0 --show-errors 0` with `PYTHONPATH=C:\Users\jkino\Documents\GitHub\RAD-Parser\src`.
- Routes evaluated: 16010.
- Pass: 14943.
- Referred: 205.
- Missing denial: 0.
- Time-conditioned missing denial: 81.
- SRD reason represented: 5.
- Conditional SRD not asserted: 776.
- Unexpected denial: 0.
- Eval errors: 0.
- Closeout non-failure: 16010/16010 (100.0%).

## 2026-07-13 - Note 516 time boundary correction (VFP-426)

- Source file: `in.json` (AIRAC 2607 SOURCE OF TRUTH).
- Backup: `in.json.pre-note516-boundary-fix.20260713-093555.bak`.
- Audit: `in.note516_boundary_fix.20260713-093555.json`.
- Corrected all seven Note 516 complement-ban entries from `0801-1659` to `0801-1700`.
- Reason: the note states availability is `1700-0800 Summer UTC`; under the RAD boundary convention the represented window starts one minute past and finishes on the hour, so the unavailable complement ends at `1700`.

## 2026-07-13 - Full source/parser rerun after Note 516 boundary fix

- Entry path: source/parser rerun after `in.json` source-data edit for VFP-426.
- SOURCE OF TRUTH `in.json`: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\in.json`.
- LEGACY WORKING COPY `in.json`: `C:\Users\jkino\Desktop\SRD Testing Files\in.json`.
- Working copy refreshed from source and hash-matched: `EB3D5D7086CA8672E44E2B294631583AC61BC74615EF9464AE3092DE0F85536F`.
- Source lint command: `python C:\Users\jkino\Documents\GitHub\New-SRDParser\Tools\lint_in_json.py --in-json ...\vFPC 2607\in.json --notes-csv ...\vFPC 2607\Notes.csv --routes-csv ...\vFPC 2607\Routes.csv`.
- Source lint result: `0 error(s), 0 warning(s)`.
- Parser command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2607` from `C:\Users\jkino\Documents\GitHub\New-SRDParser`.
- Parser result: completed; schema validation passed; structural lint passed; MC resolved `4399/4399`; `seg_not_found_total_rows=0`; generated constraints `14316`.
- Parser generated output: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- Hub diagnostic copy: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.
- Hub diagnostic SHA-256: `B5E9026A58426166CAAA8140BA96B7B23C548BDCCCCFA473809EE50627C17A94`.
- Bulk evaluator command: `python scripts\bulk_evaluate_srd.py --airac 2607 --classify-srd-reasons --trace-out data\local\2607\tmp\full_trace_2607_after_note516_boundary_fix.jsonl --summary-json data\local\2607\tmp\full_trace_2607_after_note516_boundary_fix.summary.json --show-failures 0 --show-errors 0`.
- Bulk evaluator result: 16010 routes; 14943 pass; 205 referred; 0 missing denial; 81 time-conditioned missing denial; 5 SRD-reason represented; 776 conditional SRD not asserted; 0 unexpected denial; 0 eval errors. Overall harness PASS remains `NO` because context-deferred zero-target residual buckets are still reported.
- Trace SHA-256: `F03720F7307807695E0D956478EF9E89CCE8AE5FF55A4297A287E4EA9A2E3DC9`.
- Summary SHA-256: `E8A956F6C4BB93B279C4972D3D30DE926E913D405C482640FAFC225AAD904B8F`.
- Repro manifest generated: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\repro_manifest.json`.
- Promotion status: not promoted to `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\out.json`; Ari approval still required before production candidate promotion.

## 2026-07-13 - Production-ready candidate promotion

- Ari approved promotion of the verified AIRAC 2607 parser candidate to the historical production-ready folder for later server upload.
- Promoted production candidate: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2607\out.json`.
- Source parser candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- Hub diagnostic input: `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2607\routes\out.json`.
- SHA-256 for all three copies: `B5E9026A58426166CAAA8140BA96B7B23C548BDCCCCFA473809EE50627C17A94`.
- Cycle manifest written beside the source files: `airac_manifest.json` and `airac_manifest.md`.
- Server upload status: pending Ari upload later; archive status: pending upload success.

## 2026-07-13 - SRD workbook What's New worksheet closeout

Source workbook: `UK and Ireland SRD 09 July 2026_Excel and Notes.xlsx`.
Worksheet: `What's New`.
Header: `What's New - 9th July 2026 AIRAC`.

Reviewed worksheet items against authoritative AIRAC 2607 `Routes.csv`, `Notes.csv`, and `in.json` after final curation and production-ready promotion.

New Routes:
- `ALOTI/KLONN/NIVUN/BEREP - EGCC/EGGP via TILNI`: present in current `Routes.csv`; 11 matching rows.
- `ALOTI/KLONN/NIVUN/BEREP - EGNM via NATEB`: present in current `Routes.csv`; 4 matching rows.
- `PD/PE/PF/PH - EGBJ via EGPXFRA`: interpreted as `EGPD/EGPE/EGPF/EGPH`; present in current `Routes.csv`; 5 matching rows using `<FRA>`.
- `EGLL - Various via SAM (Farnborough Airshow 2026 Routing, EXARO N514 ADKIK not available)`: present in current `Routes.csv`; 64 EGLL via SAM rows carry Note 524.
- `Note 524 (Farnborough Airshow 2026)`: present in `Notes.csv`; coded in `in.json` as comment-only/informational following the Note 512 precedent; 64 route references in `Routes.csv`.
- `EGPF/PH-LONAM/RENEQ/GODOS/TOPPA via P600 ASNUD`: present in current `Routes.csv`; 8 matching rows.

Amended Routes:
- `EGGW-EGPD`: present in current `Routes.csv`; 6 rows.
- `EGKK - EGPC`: present in current `Routes.csv`; 1 row.
- `EGBB - EGPD via KEFTE`: present in current `Routes.csv`; 2 rows.

Deleted Routes:
- `EG** - ERAKA/ADODO/BALIX/ORTAV/ATSIX/LUMEN/RATSU/MATIK/NALAN via BELOX/TUPEM`: column-aware check found 0 current rows with listed `ADES/Exit` via `BELOX` or `TUPEM`. The listed exits still appear in other valid route families, but not in the deleted BELOX/TUPEM shape.
- `EGBE Deps/Arrs due airport closure`: current `Routes.csv` contains 0 EGBE rows.

Conclusion: all AIRAC 2607 `What's New` worksheet items are represented in the current source set or intentionally comment-only. No additional source edit was required after this worksheet review.
