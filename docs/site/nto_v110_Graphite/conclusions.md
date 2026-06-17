# Graphite V11.0 — Conclusiones

**Data:** 2026-06-17  
**Modelo:** NTO V11.0  
**Material:** Graphite (G4_GRAPHITE = C)  
**Training:** 30 epochs, val_loss=-6.10

## Composición
- C: 100%
- Z_eff=6.0, A_eff=12.01, ρ=1.7 g/cm³
- n_atoms=8.53×10²² atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (C ja al catàleg) |
| material.py | ✅ mat_id=8 |
| build_dataset | ✅ 60M steps |
| train (30 epochs) | ✅ val_loss=-6.10 |
| compare | ✅ |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Métriques por energía

| Energía | edep_W1 | edep_ratio | Δd_W1 | cosθ_W1 | hits_ratio |
|---------|---------|------------|-------|---------|------------|
| 0.025eV | 0.00000 | nan | 0.018 | 0.018 | 2.86 |
| 1eV | 0.00000 | nan | 0.041 | 0.034 | 2.97 |
| 100eV | 0.00000 | 9.24 | 0.056 | 0.049 | 3.10 |
| 10keV | 0.00012 | 1.05 | 0.038 | 0.045 | 3.16 |
| 100keV | 0.00113 | 0.38 | 0.061 | 0.050 | 3.23 |
| 1MeV | 0.00514 | 0.38 | 0.204 | 0.059 | 4.11 |
| 6MeV | 0.00639 | 0.77 | 1.445 | 0.150 | 12.90 |
| 14MeV | 0.01175 | 3.97 | 3.165 | 0.568 | 5.87 |

## Análisis
- **Hits ratio**: Systemàticament alt (~3x) — el model genera masses hits
- **cosθ**: Acceptable a baixes energies (W1~0.02-0.05), degrada molt a 14 MeV (0.57)
- **Δd**: Degrada amb energia — 6 MeV té W1=1.4, 14 MeV té W1=3.2
- **edep_ratio**: nan a baixes energies (edep zero al truth)
- **Overfitting**: Moderat — el model concentra prediccions a 1-100 eV

## Conclusión
Graphite (carboni pur) té menys interaccions que PE/Paraffin perquè només té un element. El model genera ~3x més hits del que hauria, indicant que no apren bé quan aturar la cascada. Cal més dades o regularització. Les mètriques a 6-14 MeV són dolentes (pocs steps de training en aquest rang).

## Directorio de salida
`docs/site/nto/runs/nto_v110_Graphite/seed1/`
