# Paraffin V11.0 — Conclusiones

**Data:** 2026-06-17  
**Modelo:** NTO V11.0  
**Material:** Paraffin (G4_PARAFFIN = CH₂)  
**Training:** 10 epochs (timeout), val_loss=0.92

## Composición
- C: 33.3%, H: 66.7%
- Z_eff=2.67, A_eff=4.68, ρ=0.93 g/cm³
- n_atoms=1.20×10²³ atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (C+H ja al catàleg) |
| material.py | ✅ mat_id=7 |
| build_dataset | ✅ 93M steps |
| train (10 epochs) | ✅ val_loss=0.92 |
| compare | ✅ |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Métriques por energía

| Energía | edep_W1 | edep_ratio | Δd_W1 | cosθ_W1 | hits_ratio |
|---------|---------|------------|-------|---------|------------|
| 0.025eV | 0.00000 | 17.81 | 0.006 | 0.008 | 0.96 |
| 1eV | 0.00000 | 1142.73 | 0.009 | 0.007 | 0.97 |
| 100eV | 0.00000 | 40.96 | 0.010 | 0.007 | 0.99 |
| 10keV | 0.00002 | 1.10 | 0.010 | 0.012 | 0.98 |
| 100keV | 0.00003 | 1.06 | 0.010 | 0.005 | 0.99 |
| 1MeV | 0.00012 | 1.08 | 0.011 | 0.008 | 1.00 |
| 6MeV | 0.00024 | 0.95 | 0.054 | 0.007 | 1.48 |
| 14MeV | 0.00028 | 0.81 | 0.212 | 0.014 | 2.28 |

## Análisis
- **Hits ratio**: Excelente (0.95-1.00) per energies < 1 MeV
- **cosθ**: Molt bo (W1 < 0.015 a tot arreu)
- **Δd**: Bo a baixes energies, degrada a 6-14 MeV
- **edep_ratio**: Massa alt a baixes energies (edep gairebé zero → ratio sensible a small changes)
- **Overfitting**: Mínim — Paraffin és similar a PE (moderador d'hidrogen)

## Conclusión
Paraffin és químicament molt similar a PE (CH₂ vs CH₂). Els resultats són comparables als de PE. L'entrenament amb 10 epochs és suficient per bones mètriques a energies < 1 MeV. Per energies altes calrien més epochs o més dades.

## Directorio de salida
`docs/site/nto/runs/nto_v110_Paraffin/seed1/`
