# SRD Patch Carry-Forward Runbook

This file defines how to keep manual SRD/data curation from being lost at the
next AIRAC rollover.

The active machine-readable checklist is:

```text
srd_carry_forward_patches.json
```

`curation_notes.md` remains the per-cycle audit record. The carry-forward ledger
is the cross-cycle reminder: if a patch is still active there, it must be
reviewed before a production data run.

## When To Use This

Use this runbook after fetching a new AIRAC source set and before the first
production parser run.

Do not apply old patches blindly. Each active patch must be rechecked against
the new `Routes.csv`, `Notes.csv`, current RAD/AIP data where relevant, and the
current parser capabilities.

## Standard Workflow

1. Open `srd_carry_forward_patches.json`.
2. For every patch where `status` is `active` or `active-until-automated`, run
   the listed `match_checks` against the new cycle files.
3. Cross-check the Hub source issue ledger and current evidence before treating
   the list as complete:
   - `vFPC-Hub/Documentation/butler/srd_source_issue_ledger.json`
   - current-cycle `srd_ifpuv_evidence.json`, if a RAD/SRD probe pass exists
   - Linear `VFP-177` children and open issues with `source-audit` / `nats`
     labels
4. Apply only patches whose conditions still hold.
5. If a patch no longer applies, leave the source data unchanged and mark the
   result in the new cycle's `curation_notes.md`.
6. If source wording or route placement changed materially, stop and create or
   update the relevant Linear issue before encoding a new rule.
7. After each edit, validate JSON and rerun the parser/linter checks relevant to
   the changed file.
8. Record each applied, skipped, or retired patch in the new cycle's
   `curation_notes.md`, including:
   - carry-forward patch ID
   - files changed
   - row/note counts before and after
   - exact verification command or query
   - any backup or audit-detail file path
9. After the production candidate is accepted, update this ledger:
   - set `last_reviewed_cycle`
   - set `last_applied_cycle` for applied patches
   - change `status` to `watch` or `retired` when the stop condition is met
   - update `last_verification`

## Status Values

| Status | Meaning |
|---|---|
| `active` | Review every AIRAC and apply when the source condition still holds. |
| `active-until-automated` | Review every AIRAC until parser/schema/runtime automation replaces the manual patch. |
| `watch` | Do not normally edit; check that a stale source copy has not resurrected the old condition. |
| `retired` | Historical only; no carry-forward action. |

## Production Guardrails

- The live source set is still the per-cycle `Historical Files\vFPC {CYCLE}`
  folder described in the Hub authority map.
- The `airac-data` archive is a reviewable baseline and audit trail, not the
  live editing source during rollover.
- If `in.json` differs between the live source folder and the latest accepted
  archive, compare the relevant note blocks before running the parser.
- If a patch modifies `Routes.csv`, preserve original rows or note tokens in
  `curation_notes.md`.
- If a patch modifies `in.json`, validate the file immediately:

```powershell
$null = Get-Content -LiteralPath .\in.json -Raw | ConvertFrom-Json; 'valid json'
```

## Minimum Pre-Production Checklist

Before declaring a new AIRAC production candidate ready:

- every non-retired ledger entry has an entry in the cycle's
  `curation_notes.md`;
- `in.json` validates as JSON;
- New-SRDParser linter/schema checks pass;
- parser output was regenerated from the patched source set;
- API/plugin smoke checks were run for any patch that changes output shape or
  route applicability.
