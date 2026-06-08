# AIRAC 2605 manual curation note

Date: 2026-05-29

Purpose: remove SRD route rows that current RAD/IFPUV evidence rejects under EG2444A before rerunning New-SRDParser.

Reason: the following route fragments are not present in the current AIRAC and make the affected SRD rows illegal:

- `MID Y803 SFD M605/UM605 to XIDIL for DEP EGGW/EGWU`
- `EG2444A NOT AVBL FOR TFC via MID Y803 SFD`
- `IFPUV probe rejected representative EGGW rows by EG2444A`

Removed rows from `Routes.csv`:

- Worksheet line 7198 (`VFP-60-EGGW-RODNI-UM605`)
  Route: `N27 ICTAM Q63 CPT DCT MID Y803 SFD UM605`
  Remarks: none
  Defect: Current EG2444A RAD rule says MID Y803 SFD is not available for DEP EGGW/EGLL/EGWU traffic; representative IFPUV evidence rejects the EGGW UM605 row by EG2444A.
  Original CSV row: `EGGW,RODNI,245,295,N27 ICTAM Q63 CPT DCT MID Y803 SFD UM605,,XIDIL,`

- Worksheet line 7200 (`VFP-60-EGGW-RODNI-M605`)
  Route: `N27 ICTAM Q63 CPT DCT MID Y803 SFD M605`
  Remarks: none
  Defect: Current EG2444A RAD rule says MID Y803 SFD is not available for DEP EGGW/EGLL/EGWU traffic; representative IFPUV evidence rejects the EGGW M605 row by EG2444A.
  Original CSV row: `EGGW,RODNI,MC,245,N27 ICTAM Q63 CPT DCT MID Y803 SFD M605,,XIDIL,`

- Worksheet line 21193 (`VFP-60-EGWU-CPT-M605`)
  Route: `DCT MID Y803 SFD M605`
  Remarks: none
  Defect: Current EG2444A RAD rule says MID Y803 SFD is not available for DEP EGGW/EGLL/EGWU traffic. EGWU is explicitly in the same RAD branch; this row is treated as the same SRD/RAD source disagreement pending NATS clarification.
  Original CSV row: `EGWU,CPT,MC,245,DCT MID Y803 SFD M605,,XIDIL,`
