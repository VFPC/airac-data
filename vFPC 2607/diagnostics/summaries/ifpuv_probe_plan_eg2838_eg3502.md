# IFPUV Probe Plan: AIRAC 2607 EG2838 and EG3502

Purpose: confirm whether the two remaining AIRAC 2607 SRD/RAD residuals are true
RAD authority, evaluator/source modelling debt, or source curation candidates.

## EG2838: EGAE to EGTE via MAKUX SOSIM Q38 MALUD

Question: did AIRAC 2607 legitimately change EG2838 section 2 from a
`Q38 SOSIM-GIGTO` segment-specific level gate to an `AT SOSIM` level gate?

Relevant local facts:

- 2606 generated row: EGAE -> EGTE, `MAKUX SOSIM Q38 MALUD ...`, min FL255,
  max open-ended.
- 2607 generated row: EGAE -> EGTE, same route, min FL255, max FL335.
- 2606 EG2838 compiled section 2 level gate location:
  `Q38 SOSIM-GIGTO`.
- 2607 EG2838 compiled section 2 level gate location:
  `SOSIM`.
- Current 2607 evaluator denies at high level under EG2838.

### P2838A: residual route, high level

Expected question: does IFPUV cite EG2838, or a different authority, for the
exact residual shape?

```text
(FPL-P2838A-IS
-B738/M-SDFGHIRWY/LB1D1
-EGAE1200
-N0450F290 BELZU L10 DUFFY DCT MAKUX DCT SOSIM Q38 MALUD L15 RISLA N862 KARNO DCT ALHUP DCT WEVBE DCT ABWIV N862 PACSE N92 EXMOR DCT TIVER
-EGTE0130
-PBN/B1D1O1S1 DOF/260712 REG/J2838A)
```

Interpretation:

- If IFPUV cites EG2838, treat the source row as RAD-denied above FL285.
- If IFPUV cites another authority, keep as evidence classification, not
  source mutation, unless the other authority is also source-expressible.
- If IFPUV accepts, investigate the 2607 evaluator/compiler EG2838 section 2
  location.

### P2838B: same route, below FL285 control

Expected question: does the route pass below the EG2838 high-level gate?

```text
(FPL-P2838B-IS
-B738/M-SDFGHIRWY/LB1D1
-EGAE1200
-N0450F270 BELZU L10 DUFFY DCT MAKUX DCT SOSIM Q38 MALUD L15 RISLA N862 KARNO DCT ALHUP DCT WEVBE DCT ABWIV N862 PACSE N92 EXMOR DCT TIVER
-EGTE0130
-PBN/B1D1O1S1 DOF/260712 REG/J2838B)
```

Interpretation:

- EG2838 should not deny solely on section 2 below FL285.

### P2838C: 2606-style high-level alternate route

Expected question: does IFPUV accept the high-level route that avoids
`SOSIM Q38 MALUD` and uses the 2606 generated high-level shape?

```text
(FPL-P2838C-IS
-B738/M-SDFGHIRWY/LB1D1
-EGAE1200
-N0450F290 BELZU L10 DUFFY DCT INKOB Q39 NOMSU UQ4 WAL N862 KARNO DCT ALHUP DCT WEVBE DCT ABWIV N862 PACSE N92 EXMOR DCT TIVER
-EGTE0130
-PBN/B1D1O1S1 DOF/260712 REG/J2838C)
```

Interpretation:

- If accepted, source curation should likely split/cap the `SOSIM Q38 MALUD`
  high-level row rather than suppress all EGAE -> EGTE high-level routing.

### P2838D: high-level exception-arrival control

Expected question: does one of the explicit EG2838 section 2 exception arrivals
avoid EG2838 denial above FL285?

```text
(FPL-P2838D-IS
-B738/M-SDFGHIRWY/LB1D1
-EGAE1200
-N0450F290 BELZU L10 DUFFY DCT MAKUX DCT SOSIM Q38 MALUD
-EGNM0130
-PBN/B1D1O1S1 DOF/260712 REG/J2838D)
```

Interpretation:

- EG2838 section 2 has exception arrivals EGCC/EGCN/EGNM/EGNX. A denial here
  would suggest the exception handling or route completion needs review.

## EG3502: EGLL ULTIB to EGNX via T420 WELIN

Question: does IFPUV treat the SRD row as valid when the HEMEL STAR context is
present, or is `T420 WELIN` denied regardless?

Relevant local facts:

- 2606 and 2607 both generate EGLL ULTIB -> EGNX via `T420 WELIN`, min FL115,
  max FL195.
- Source row carries STAR `HEMEL1E`.
- 2606 evidence classified this as `modelling_debt_investigate`, not confirmed
  authority.
- EG3502 text says traffic should connect to the HEMEL STAR at HEMEL.

### P3502A: residual route, STAR context supplied

Expected question: if the IFPUV tool allows STAR selection, does HEMEL1E make
this route valid?

```text
(FPL-P3502A-IS
-B738/M-SDFGHIRWY/LB1D1
-EGLL1200
-N0450F190 ULTIB T420 WELIN
-EGNX0100
-PBN/B1D1O1S1 DOF/260712 REG/J3502A)
```

Additional IFPUV form/context:

- SID: ULTIB, if separately selectable.
- STAR: HEMEL1E, if separately selectable.

Interpretation:

- If accepted with STAR HEMEL1E, this is evaluator diagnostic modelling debt:
  the evaluator must make declared/source STAR context decisive for EG3502.
- If denied under EG3502 even with HEMEL1E, the SRD row is probably not a clean
  static output route.

### P3502B: same route, no STAR context

Expected question: does IFPUV cite EG3502 when HEMEL1E is not supplied?

```text
(FPL-P3502B-IS
-B738/M-SDFGHIRWY/LB1D1
-EGLL1200
-N0450F190 ULTIB T420 WELIN
-EGNX0100
-PBN/B1D1O1S1 DOF/260712 REG/J3502B)
```

Interpretation:

- A denial here but pass in P3502A confirms the STAR-context modelling gap.

### P3502C: destination control outside Midlands group

Expected question: does the EG3502 denial depend on Midlands-group arrival
scope?

```text
(FPL-P3502C-IS
-B738/M-SDFGHIRWY/LB1D1
-EGLL1200
-N0450F190 ULTIB T420 WELIN
-EGNT0100
-PBN/B1D1O1S1 DOF/260712 REG/J3502C)
```

Interpretation:

- EG3502 should not be the denial authority unless EGNT is being treated as a
  Midlands-group arrival by the relevant RAD grouping.
