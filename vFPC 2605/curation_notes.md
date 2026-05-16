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
