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
