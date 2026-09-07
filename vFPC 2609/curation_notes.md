## AIRAC 2609 SRD carry-forward curation (20260906-190302)

Source: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\Routes.csv`
Backup: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\Routes.csv.pre-2609-srd-carry-forward-curation.20260906-190302.bak`
Audit detail: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\Routes.srd_carry_forward_curation.20260906-190302.json`

Re-tested every recurring carry-forward class from `Documentation/butler/airac_curation_ledger.md`
and the AIRAC 2607 `curation_notes.md` against the fresh 2609 `Routes.csv` and a freshly
generated `aip_segments.json` (AIP Parser run against 2609 eAIP ENR-3.2/3.3) before reapplying:

- SRD-CF-001 illegal ATS-route withdrawals: 14 rows removed (`UNTAL N110`: 2, `BATLI Y250 OBOXA`: 12).
  Verified via fresh `aip_segments.json`: `N110` segments are `DOLAS-ABTOS-ESHIN-USEKA-ERKIT` (no
  `UNTAL`); `Y250` segments are `DAVENTRY-AKUPA-LESTA-MAMUL-UPTON-BATLI-RIMTO-KEFTE-OBOXA-GASKO`
  (`BATLI` and `OBOXA` are not adjacent; `RIMTO`/`KEFTE` sit between them).
- SRD-CF-003 MEDOG DCT VATRY direct-route withdrawals: 137 rows removed (identical count to 2607).
  `MEDOG` has zero airway segments in the fresh AIP data (DCT-only point), consistent with the
  original IFPUV DCT-distance rejection (VFP-401). Not re-probed via IFPUV this cycle; carried
  forward on unchanged AIP topology grounds.
    10|- SRD-CF-004 PEMOB DCT VATRY repair: 162 rows changed to `PEMOB M17 VATRY`, only within the full
  `MEDOG DCT KRAGY DCT PEMOB DCT VATRY` family. `M17` still connects `PEMOB-LEMGU-VATRY` in fresh
  AIP data. Left the 1 parked non-KRAGY `PEMOB DCT VATRY` row (EGTO) unmutated, per precedent.
- Note 516 misplaced route-note token removal: 1 row (`AGORI->EGKB` via `VAMEB UL612 LISTO`,
  `STAR LISTO1C`) — same exact row recurred from 2607.
- Note 339 misplaced route-note token removal: 3 rows (2607 had 4; one of the four route
  variants no longer appears in the 2609 source). Preserved co-located Notes 450/453/270 on the
  affected rows.
- Note 480 DVR L10 RINTI FL-band repair: 32 rows set to Min 115 / Max 245 (2607 had 41; fewer
   20|  route variants this cycle). `ADES/Exit == RINTI` + `DVR` in `Route` was the match key (RINTI is
  the exit-point column, not part of the `Route` string).

Rows before/after: `32376` -> `32225`.

Not yet re-tested this cycle (RAD-rule-derived classes; deferred to the RAD-Parser + bulk
evaluator pass rather than manually reapplied from 2607's JSON evidence, since `RAD_2609_v1_9.xlsx`
may have changed the underlying rule conditions):

- RAD static-output curations: EG3499 WEVBE, EG4121 DVR L10 RINTI cap, EG2269 NIRIF, EG2640
  EGJJ-EGPK BLACA, EG2983 Q63 VATRY, EG3217 CARWI, EG3619 EGPT INREV.
- RAD residual curation: EG2608/EG2628, EG2444, EG2721, EG2328, EG3474/EG5793, EG2651, EG4171.
- Final IFPUV-backed curation: EG2838, EG3502.
- RAD Annex 2A city-pair cap plan (2607 applied 324 row max-level lowerings from a regenerated
  plan; needs regeneration against `RAD_2609_v1_9.xlsx`).

These will be re-tested by running the RAD-Parser exports fresh against `RAD_2609_v1_9.xlsx`,
assembling the Hub bundle, and reading the `unexpected_denial` bucket from the bulk evaluator
rather than reapplying old removals blindly.

## AIRAC 2609 Note 524 status check (20260906)

Note 524 ("Farnborough Airshow 2026 routing") still tags 64 `EGLL` via `SAM` route rows in the
2609 source (same count as 2607). The note text still reads "13th - 24th July" — that window has
already passed relative to AIRAC 2609 (2026-09-03 to 2026-09-30). No source or `in.json` change
made: the carried-forward `in.json` already codes Note 524 as comment-only/informational
following the Note 512 precedent, and the stale dates do not change vFPC's static output (VATSIM
never modelled the airshow anyway). Worth a NATS query eventually that the note appears to be a
stale carry-forward in their own source, but not a vFPC release blocker.

## AIRAC 2609 What's New worksheet closeout (20260906)

Source workbook: `UK and Ireland SRD_03 September 2026_Excel and Notes.xlsx`.
Header: `What's New - 3rd September 2026 AIRAC`.

- New Routes: `AMPOP-EGNM` (1 row present); `EIKN-EGNX via DEXEN` (1 row present).
- Amended Routes: `EGFF/EGGD-EGPD/EGPE/EGPF/EGPH/EGPK/EGPN/EGPT` (all pairs present, 4-8 rows
  each); `EGTE-EGPD/EGPE/EGPF/EGPH/EGPK` (all pairs present, 4-8 rows each); Notes 512, 524
  (both already correctly coded comment-only, see above).
- Deleted Routes: `EGFF/EGGD-EGPF/EGPK FL245-255 via POL` — confirmed 0 rows remain at that
  FL band for those pairs; other FL bands via POL remain, as expected.

Conclusion: all AIRAC 2609 `What's New` worksheet items are represented correctly in the
current source set. No additional source edit was required from this review.

## AIRAC 2609 RAD-Parser exports and Hub bundle assembly (20260906)

- RAD-Parser exports run from a clean detached worktree (`RAD-Parser-clean-2609`, main HEAD
  `8920819`) rather than the primary clone, which currently carries Ari's uncommitted geometry
  generalization / Annex 3A evaluator WIP (confirmed not required for this rollover).
- Workbook: `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\RAD_2609_v1_9.xlsx`.
- Compiled: Annex 2A 190, Annex 2B 1166/1 skipped, Annex 2C 404, Annex 3A conditions 4, Annex 3A
  ARR 164/34 skipped, Annex 3A DEP 118/41 skipped, Annex 3B FRA 10, Annex 3B DCT 687/617 skipped
  (freely available), Annex 1 groups 118.
- AIP Parser run fresh for 2609: 220 airways/1220 segments (0 problems), 463 airspace layers, 54
  navaids, 1404 restricted/danger/military areas, 116 airports (0 skipped).
- ENR 4.4: UK 1062 points (0 warnings), France sidecar generated. No Irish ENR 4.4 source this
  cycle (AirNav Ireland portal did not expose eAIP HTML for 2609; IAIP package page PDF link
  fetch also failed — same `[RULE:IRISH-EAIP-ENR44-URL]` failure mode as prior cycles).
- Created `config/rad/restricted_areas_active.2609.json` carried forward unchanged from 2607
  (`{"active_areas": ["EGD036"]}`; identical across 2605/2606/2607).
- `python scripts\assemble_bundle.py --airac 2609`: 2743 total rules, 118 Annex 1 groups (30
  referenced, all present), 459 named airspaces, 1 composite sector alias, FRA polygons
  `EGPXFRA`/`EGTTFRAW`, ENR 4.4 2898 points/1119 FRA waypoints/412 classified, 2914 waypoints/220
  airways, 116 airports (stub), 1 active restricted area. NATS sector polygons not present
  (optional, skipped, consistent with 2607).

## AIRAC 2609 parser candidate generation (20260906-1912)

- Updated `New-SRDParser` `PathResolver.BuildDebugPaths` sector-file filename from
  `UK_2026_07.sct` to `UK_2026_09.sct` (PR VFPC/New-SRDParser#203, merged).
- Working copy refreshed from source to `C:\Users\jkino\Desktop\SRD Testing Files` before parser
  run; `in.json` SHA-256 hash-matched against source
  (`EADE5C57EBF596713430F42E6E0917C48FB1EB8A1C43A3276DD1D134D90F405F`).
- Command: `dotnet run --project NewSRDParser\NewSRDParser.csproj -c Debug -- DEBUG_MODE=TRUE CYCLE_OVERRIDE=2609`.
- Result: schema validation passed; structural lint 0 errors/0 warnings; MC resolved 4408/4408
  (100%); `seg_not_found_total_rows=0`; AIRAC cycle correctly reported as `2609`; 18390 raw
  constraints built, remerged to 14147, consolidated to 13139.
- Known recurring non-blocking log lines (present identically in 2607's final production run,
  `log_13072026_1642.txt`): `SctParser: Fix 'WTN' appears 2 times` and `FixResolver: Missing fix
  'KEFTE<FRA>'`. Not new to 2609; not investigated further this cycle.
- Generated candidate: `C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`.
- SHA-256: `840F311E5A27D930234DA15768EB77A1571792439BDB7956EFB6AFFC9E071322`.
- Candidate copied for Hub diagnostics to `C:\Users\jkino\Documents\GitHub\vFPC-Hub\data\local\2609\routes\out.json`
  (hash-matched).

## AIRAC 2609 Note 525 addition (20260906)

- Source file: `in.json` (AIRAC 2609, carried forward from 2607; new note this cycle).
- `lint_in_json.py` flagged `NOTE_MISSING_FROM_IN_JSON` / `ROUTE_NOTE_MISSING_FROM_IN_JSON` for
  note 525, which is new in the 2609 `Notes.csv` (`EGNC unlicensed`).
- Route placement: 1 row (`EGNC -> NAVPI`, co-tagged with Notes 467/523).
- Note text: "EGNC is currently an unlicensed aerodrome. The expectation is that traffic filing
  these routes will initially depart VFR."
- Decision: comment-only. Aerodrome licensing status and an expected initial VFR departure
  procedure are real-world operational state that vFPC/VATSIM does not model (same class as
  Notes 506/507/520-523); no static condition the plugin can check.
- Added to `in.json`: `{"note": 525, "comment": "Not encoded: EGNC aerodrome licensing status
  (unlicensed) and the expected initial VFR departure are real-world operational state;
  vFPC/VATSIM does not model aerodrome licensing status or require VFR-departure sequencing for
  this route."}`.
- `lint_in_json.py` result after the addition: `0 error(s), 0 warning(s)` (286 note entries, 214
  unique notes in `in.json`, 210 note headers in `Notes.csv`).

## AIRAC 2609 bulk evaluator baseline round 1 (20260906-1922)

`data/local/2609/tmp/full_trace_2609_initial.jsonl` (+ `.summary.json`), 15598 routes evaluated:
Pass 14741 (94.6%), Referred 89, `unexpected_denial` **230**, `srd_conditional_not_asserted` 510,
`time_conditioned_missing_denial` 25, `srd_reason_represented` 3, eval errors 0.

Classified the 230 `unexpected_denial` routes by denying RAD rule ID against real trace evidence
(`vfpc-use-real-routes`): all but one rule ID matched a known carry-forward curation class already
documented and JSON-ledgered for AIRAC 2607 (same route families, same RAD rule text confirmed
fresh via `explain_rule.py` against `RAD_2609_v1_9.xlsx`) plus a fresh Annex 2A city-pair cap
violation set (new every cycle, regenerated from the current workbook, not carried forward).

## AIRAC 2609 RAD Annex 2A city-pair cap curation (20260906-1918)

- Ran `scripts/check_eg_city_pair_caps.py` via an in-memory `importlib` path override (script has
  hardcoded `data/local/2604` paths; no CLI override exists) pointed at
  `data/local/2609/rad/annex2a_runtime_rules.json` / `annex1_groups.json`, writing
  `data/local/2609/rad/city_pair_cap_compliance.json` (9002 EG routes checked, 86 caps with EG
  matches, 646 flagged route/cap pairs; note the JSON's internal `"airac"` field is a hardcoded
  literal `"2604"` in the script — cosmetic only, does not affect which files were read/written).
- Verdict breakdown across all 646 flagged entries: `VIOLATION` 324, `UNCERTAIN_AIRSPACE` 243,
  `POSSIBLE_EXCEPTION` 62, `UNCERTAIN_CAPABILITY` 17 — closely matching 2607's precedent
  (324/243/17 identical; 62 vs 69 `POSSIBLE_EXCEPTION`, normal cycle-over-cycle route churn).
- Applied only the 324 confirmed `VIOLATION` entries: for each, matched the exact
  `(adep, ades, route, star, remarks)` tuple against `Routes.csv` and lowered `Max` to the rule's
  `cap_fl`. 324/324 matched cleanly, 0 unmatched.
- Backup: `Routes.csv.pre-city-pair-cap-curation.20260906-191851.bak`.
- Plan: `Routes.city_pair_cap_edits.20260906-191851.json`.
- Left `POSSIBLE_EXCEPTION` / `UNCERTAIN_AIRSPACE` / `UNCERTAIN_CAPABILITY` untouched, per 2607
  precedent (these require case-by-case RAD-text/IFPUV review, not a blanket cap).

## AIRAC 2609 RAD static-output / residual / final-IFPUV curation replay (20260906-1924)

Rather than re-deriving match logic from scratch (2607's raw trace-based route/FL substring
matching proved fragile against DCT-token and FL-band-split differences between the two out.json
builds — see abandoned dry-run attempts), replayed the **exact 2607 curation ledgers** directly
onto 2609's `Routes.csv`, matching each ledger entry's recorded `(ADEP/Entry, ADES/Exit, STAR,
Route)` tuple verbatim against current rows and applying the same `remove` / `cap_max` action:

- `Routes.rad_static_output_curation.20260712-210056.json` (2607; EG3499 WEVBE, EG2269 NIRIF,
  EG2983 Q63 VATRY, EG3619 EGPT INREV, EG4121 DVR L10 RINTI, EG2640 EGJJ-EGPK BLACA, EG3217 CARWI).
- `Routes.rad_residual_curation.20260712-211449.json` (2607; EG2608/EG2628, EG2444, EG2721,
  EG2328, EG3474/EG5793, EG2651 Midlands DET L6 DVR cap).
- `Routes.final_ifpuv_curation.20260712-213851.json` (2607; EG2838 SOSIM Q38 cap, EG3502 HEMEL
  T420 WELIN removal).

Result: 260 ledger actions replayed, **220 removed / 36 capped / 4 not matched** (98.5% match
rate). The 4 unmatched entries were all EG3217 CARWI `Min=285/Max=660` duplicate rows that 2607
had twice (near-identical entries with slightly different route text) but 2609's source only
carries once — a genuine SRD-side de-duplication between cycles, not a curation-tooling gap.
Rows before/after: `32225` -> `32005`.

- Backup: `Routes.csv.pre-rad-ledger-replay.20260906-192446.bak`.
- Plan: `Routes.rad_ledger_replay.20260906-192446.json`.

Refreshed the `SRD Testing Files` working copy, re-ran `New-SRDParser`
(`CYCLE_OVERRIDE=2609`, schema/lint clean, MC 100%, 12979 constraints), copied to
`data/local/2609/routes/out.json`, and re-ran the bulk evaluator
(`full_trace_2609_round2.jsonl` / `.summary.json`): 15382 routes, `unexpected_denial`
**230 -> 5**, `time_conditioned_missing_denial` unchanged at 25.

### Round-2 residual: 5 remaining `unexpected_denial` routes (real-route triage)

- **EG3217 CARWI** (4 routes: `EGTE -> EGPD/EGPF/EGPH/EGPK` via `... UP17 POL UN601 RIBEL <FRA>
  ...`, `Min=255/Max=285`) — a surviving duplicate of an already-curated route family that the
  2607-ledger replay's exact-string match didn't consume (different DCT-token placement / an
  extra un-enumerated duplicate not present in the 2607 ledger's 22 entries). Removed directly
  from the live 2609 `Routes.csv`, matching the same confirmed EG3217 remove-only precedent.
- **EGLF1034** (`EGJJ BENIX -> EGKK via G27 NEVIL`, `Min=105/Max=195`) — **new for this cycle**,
  not a 2607 carry-forward. `explain_rule.py` confirms current RAD text: `NOT AVBL FOR TFC ...
  2. ARR (EGGW, EGHH, EGHI, EGKK, EGLD, EGLL, EGSC, EGSH, EGSS, EGWU, FARNBOROUGH_GROUP,
  MIDLANDS_GROUP)` unconditionally blocks this dep/route/arr combination (rule valid since AIRAC
  2502, so pre-existing in the workbook; this specific SRD route via `G27 NEVIL` simply wasn't
  exercised as an unexpected denial in the 2607 baseline). Removed as a new static-output
  curation; flagged here distinctly from the carry-forward set for visibility.
- Backup: `Routes.csv.pre-final-round2-curation.20260906-193039.bak`.
- Plan: `Routes.final_round2_curation.20260906-193039.json`.
- Rows before/after: `32005` -> `32000`.

Refreshed the working copy again, re-ran `New-SRDParser` (clean, 12974 constraints), re-copied to
Hub, and ran a final bulk evaluation (`full_trace_2609_final.jsonl` / `.summary.json`): 15377
routes, **`unexpected_denial` = 0**, `missing_denial` = 3, `time_conditioned_missing_denial` = 25
(unchanged), `eval_errors` = 0, closeout non-failure 100.0% (15374/15377).

### "Hunh, that's odd" investigated and resolved: 3 `missing_denial` routes

Round 2 introduced 3 `missing_denial` classifications
(`EGJJ SKERY -> EGBB via ... GROVE` x2, `EGTE -> EGBB via EXMOR CARWI N91 FIGZI`) that were
**not** present in round 1 (round 1 classified the same 3 routes as `srd_reason_represented`,
i.e. an accepted/neutral bucket). Investigated before accepting, per the "ask when stuck" /
"hunh, that's odd" rule, since a metric moving the wrong direction after a curation pass is
exactly the kind of signal that should not be papered over.

Root cause confirmed **not** a real data/rule regression: the `Routes.csv` rows generating these
3 SRD test routes are byte-identical before/after the curation (checked directly), and the
`eval_results` (every RAD rule outcome) and the SRD `ban`/`alerts` fields in the trace are
byte-identical between round 1 and round 2 for all 3 routes — only the internal
`out_json_constraint_path` / `route_index` shifted (as expected, since removing ~256 rows
upstream shifts every subsequent constraint's position). `classify_srd_constraint_representation`
in `lib/bulk_srd_reason_representation.py` uses `route_index` to look up the generating
`out.json` constraint group in a flattened list; this lookup is index-position-sensitive and
appears to not always survive a route-count shift cleanly for these 3 routes, silently falling
back to the stricter `missing_denial` bucket instead of `srd_reason_represented`. This is a
**harness/diagnostic-tooling artifact**, not a RAD/SRD data problem — the underlying constraint
and its RAD-rule evaluation are correct and unchanged. Not fixed this session (out of scope for
the rollover); worth a follow-up look at `_related_constraint_group_for_srd_reason` if it recurs.

## AIRAC 2609 bulk evaluator final state (20260906-1931)

`data/local/2609/tmp/full_trace_2609_final.jsonl` (+ `.summary.json`):

| Metric | Value |
| --- | --- |
| Total routes evaluated | 15377 |
| Pass | 14750 (95.9%) |
| Referred (exits UK FIR) | 89 (0.6%) |
| Missing denial | 3 (harness artifact, see above; real routes unaffected) |
| — time_conditioned_missing_denial | 25 (unchanged from round 1) |
| Conditional SRD not asserted | 510 (profile/time not supplied; zero-target residual) |
| **Unexpected denial** | **0** |
| Eval errors | 0 |
| Closeout non-failure | 100.0% (15374/15377) |

`data/local/2609/routes/out.json` (SHA-256 to be recorded in the reproducibility manifest) is the
verified diagnostic candidate. Final `Routes.csv` row count: `32000` (from `32376` at start of
cycle, after all curation classes above).

## AIRAC 2609 out.json staged + manifests generated (20260906-1935)

- Copied the verified diagnostic candidate
  (`C:\Users\jkino\Desktop\SRD Testing Files\output files from SRD Parser\out.json`,
  SHA-256 `77D5DF6F8D6B035690A0E54DDDDE42C75A1840A6969EB9E803140730C9D14D6F`) into the production
  slot `Historical Files\vFPC 2609\out.json` for Ari's review, consistent with the 2607
  precedent of staging the candidate in place before formal sign-off. **This has not been
  through the formal Step 4 human-review gate for production upload / API release** — treat as
  staged, not promoted, until Ari explicitly confirms.
- Ran `scripts\airac_cycle_manifest.py --airac 2609 --source-root "...vFPC 2609"`: wrote
  `airac_manifest.json` / `.md` beside the source files; out.json hash matches the Hub
  diagnostic input exactly.
- Ran `scripts\airac_repro_bundle.py --airac 2609 --source-root "...vFPC 2609" --output
  data\local\2609\repro_manifest.json`: 18/20 expected artifacts present. Missing:
  `evidence\airac\2609\srd_ifpuv_evidence.json` and its legacy fallback — expected-missing
  this cycle since no fresh IFPUV probes were run (all curation was SRD-only, RAD-ledger-replay,
  or directly RAD-text-confirmed, not IFPUV-probe-derived).
- **Outstanding before this cycle can be called "released"**: Ari's explicit promotion
  confirmation, UKVFPCAPI upload, and `airac-data` archival (production-promotion checklist
  items 5-6 in `airac_rollover_run_rules.md`) — none of these have been done.

## AIRAC 2609 UKVFPCAPI upload + post-upload verification (20260907-1151)

- User approved proceeding without a separate Ari cross-check: "we didn't do any
  re-programming and this appears to have been a vanilla rollover using techniques that
  have proved themselves previously, including removing the excel bloat."
- Pre-upload cycle-tag question raised and resolved: despite three parser builds this
  cycle (post SRD-only curation, post RAD-cap/ledger-replay curation, post round-2
  residual curation), all curation happened **before** any upload, so this is a single
  release for the cycle, not a `curate_routes.py`-style post-upload correction. Verified
  against the 2607 precedent (`vFPC 2607\out.json` also used a bare `"2607"` cycle despite
  extensive pre-upload RAD curation) — bare `"2609"` is correct, no dot-revision needed.
- First upload attempt failed: `RULE:UPLOAD-CYCLE-FORMAT: cycle "" must be a 4-digit AIRAC
  identifier...`. Root cause: user error, wrong file selected in the admin panel file
  picker (not a code or data problem — the promoted candidate's `cycle` field was
  confirmed unchanged and correct, `"2609"`, immediately before and after this attempt).
  Re-uploaded the correct file:
  `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\out.json`
  (SHA-256 `77D5DF6F8D6B035690A0E54DDDDE42C75A1840A6969EB9E803140730C9D14D6F`).
- Upload accepted. Post-upload verification (`/version` + `/full`, per
  `out_json_release_runbook.md`):

  | Check | Production | Live |
  | --- | --- | --- |
  | cycle | `2609` | `2609` |
  | airports | 89 | 89 |
  | constraints (sum) | 12974 | 12974 |
  | per-airport constraint diffs | — | 0 |

  `/version` reports `last_updated_date: 07/09/2026`, `last_updated_time: 11:51:14`.
- **Production promotion checklist item 5 (upload) complete.** Item 6 (archive) next.

## AIRAC 2609 airac-archiver run + Routes.csv.pre-*.bak investigation (20260907-1252)

- Ran `python -m src archive --cycle 2609` from `airac-archiver` (`config.local.yaml`
  `hub_data_root` confirmed pointed at `vFPC-Hub\data\local`). Result: 175 files + manifest
  staged in `airac-data\vFPC 2609`.
- Verified against the runbook's archive gate targets: no missing-expected-file warnings;
  `out.2609.json` archived SHA-256 matches the promoted candidate exactly
  (`77D5DF6F8D6B035690A0E54DDDDE42C75A1840A6969EB9E803140730C9D14D6F`); raw SRD/RAD workbooks,
  current `.sct`, source CSV/JSON, AD2/eAIP inputs, Hub `bundle/`/`rad/`, `diagnostics/summaries/`,
  and `repro_manifest.json` all present.
- **Investigated an apparent gap**: the manifest contains zero `.bak` files, meaning the four
  `Routes.csv.pre-*.bak` snapshots generated during this cycle's curation are not archived.
  Traced to `airac-archiver/src/archiver.py` `_is_allowed()` / `_SOURCE_PROVENANCE_RE`: only
  `in.json.pre-*.bak` is allowlisted, no equivalent pattern exists for `Routes.csv.pre-*.bak`.
  Confirmed this is **pre-existing and deliberate**, not a 2609-specific regression: the exact
  same gap exists in the already-published AIRAC 2607 archive (its four `Routes.csv.pre-*.bak`
  files, documented in 2607's own `curation_notes.md`, were never archived and no longer exist
  locally either — a permanent, already-accepted gap for that cycle). A dedicated test
  (`test_route_backup_not_allowed`, added in the same PR #7 that allowlisted the small JSON/MD
  curation-evidence files) locks in this exclusion. Drafted a fix to add the pattern, then
  reverted it after finding this evidence — the exclusion is an intentional storage-size policy
  (small diff-evidence files archived; large multi-MB near-duplicate raw snapshots are not),
  confirmed with the user rather than silently reversed. Reworded
  `Documentation/airac_rollover_run_rules.md` and `Documentation/out_json_release_runbook.md` in
  `vFPC-Hub` (commit `9bc259a`) so this isn't re-litigated next cycle.
- Archive committed and pushed: `airac-data` commit `6f118ea`, "Archive vFPC 2609 production
  artifacts", 176 files changed.
- **Production promotion checklist complete: staged, uploaded, verified, archived.**

## Post-publication fix: SRD Note 94 (EGKK SFD SID) inverted time window — revision 2609.1 (20260907-1500)

- **Trigger:** a controller-reported RST error (via Calvin) for `EZY1234 EGKK->LEPA` via
  `SFD Y47 DRAKE L151 SITET UN859 ...` at EOBT 2200Z. The plugin denied citing "only permitted
  06:00-23:00 local", the exact opposite of vMATS's "SFD SIDs only available 2300-0600 local".
- **Root cause:** `in.json` note 94's own coded `start`/`end` (`0600`/`2300`) were inverted
  relative to every other source of truth: the note's own alert free-text ("available
  2300-0600"), the official NATS `Notes.csv` wording ("NOT AVAILABLE 0600-2300"), the UK AIP
  AD 2-EGKK chart ("Via SFD... to be used only 2300-0600 (2200-0500)"), RAD Annex 2B EG3306
  ("NUCHU DCT BOGNA/SFD Night Time Fuel Saving Routes"), and the pre-2605 hard-coded `in.json`
  encoding of the SFD point itself (`start:2300/end:0600`, correct). Confirmed against runtime
  semantics: `VFPC-next/src/TimeWindow.h checkTimeWindow()` treats a plain (non-`banned`)
  restriction's `[start,end]` as the *permitted* window. The inversion was introduced during
  the AIRAC 2605 refactor that converted the EGKK SFD/BOGNA/HARDY hard-coded airport block into
  reusable Note 94 — live in production for 3 cycles (2605, 2607, 2609) before this fix.
- **Why the linter missed it:** `lint_in_json.py`'s `ONLY_AVAILABLE_COMPLEMENT_TIME_MISMATCH`
  check (added for VFP-426) only fires when `alert.ban == True` and the alert text matches
  "only available ... HH:MM-HH:MM" (only-first phrasing). Note 94 has `warn:true` (not
  `ban:true`, it's a plain single restriction, not a ban+complement pair) and its wording is
  "available ... local only" (available-first) — both preconditions fail, so the check never
  inspects it. No existing check cross-validates a note's free-text alert time window against
  its own coded `start`/`end` for the plain-restriction shape. Flagged as a real gap in
  [VFP-434](https://linear.app/vfpc/issue/VFP-434), not separately scoped for a fix.
- **Issue filed:** [VFP-434](https://linear.app/vfpc/issue/VFP-434) — thorough writeup with the
  full evidence chain, regression history, and scope, filed before making any changes.
- **Fix applied:**
  - `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\in.json` note 94: swapped
    `start.time` `"0600"`->`"2300"` and `end.time` `"2300"`->`"0600"`. Alert text unchanged
    (it was already correct).
  - `New-SRDParser\Testing\TestData\in.json` note 94: same swap, for test-fixture consistency.
    Committed as its own PR: VFPC/New-SRDParser#206 (branch `fix/vfp-434-note94-sfd-time-window`,
    squash-merged). No test assertions reference note 94/SFD/SEAFORD directly. The 15
    pre-existing `dotnet test` failures on `main` (aggregate alert-count invariant,
    `Phase3_Statistics_AggregateMetrics`) are confirmed present with or without this change
    (verified via `git stash`) — unrelated, not investigated further here.
  - Re-linted production in.json: 0 errors/0 warnings (unchanged).
- **Regenerated `out.json`** via `New-SRDParser` with `CYCLE_OVERRIDE=2609.1` (dot-revision,
  per `Program.ParseCycleOverride` convention for same-cycle corrections) after refreshing the
  `SRD Testing Files` working copy from the fixed production inputs. Schema/lint clean, MC
  4355/4355 resolved (100%), same 12974 output constraints as the pre-fix run.
- **Verified the diff is exactly the expected 4 constraints**, nothing else moved: compared
  the new `out.json` against the pre-fix production copy constraint-by-constraint (by
  `(icao, sid.point, constraint_index)`). Diffs: exactly `EGKK/SFD` constraint indices 0, 1, 3,
  6 — all four constraints carrying the note-94 alert — each with `start`/`end` swapped as
  intended. Total constraint count unchanged (12974), airport count unchanged (89).
- **Bulk evaluator sanity check** (real routes, not synthetic): copied the new `out.json` to
  `data/local/2609/routes/out.json` and re-ran `bulk_evaluate_srd.py --airac 2609`. Result:
  15377 routes evaluated (unchanged from the final pre-fix baseline), `raw_unexpected`=0,
  `unreviewed_unexpected`=0, `eval_errors`=0, `passed`=14750, `referred`=89 — all identical to
  the pre-fix baseline, confirming no regression elsewhere. Note: the EGKK/SFD restriction is
  airport-section-coded in `in.json` (not driven by a `Routes.csv` row), so no individual
  `dep=EGKK, sid_point=SFD` trace record exists in this diagnostic tool's population — the
  isolated `out.json` constraint diff above is the direct evidence for this specific fix.
- **Promoted to production:** backed up the pre-fix `out.json`
  (`out.json.pre-note94-fix.<timestamp>.bak`), copied the `2609.1`-tagged `out.json` to
  `C:\Users\jkino\Desktop\vFPC files\Historical Files\vFPC 2609\out.json` (SHA-256
  `EB0EC278BA5144DAE0A26FCD127773E3443755EC64BF339AF1F856DB4256CAFA`), regenerated
  `airac_manifest.json`/`.md` and `in_json_lint_report.json`.
- **Next:** archive as `out.2609.1.json` via `airac-archiver`, hand off for upload (operator
  uploads personally), and send a private reply to the original reporter (Calvin's ticket) —
  **no public Discord announcement** for this fix, per operator direction.
