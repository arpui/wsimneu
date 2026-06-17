# DPE V11.0 — Conclusiones

**Data:** 2026-06-17  
**Modelo:** NTO V11.0  
**Material:** DPE (Polietilè deuterat: (²H₂C)ₙ)  
**Training:** 30 epochs, val_loss=0.25

## Composición
- D (²H): 66.7%, C: 33.3%
- Z_eff=2.67, A_eff=5.34, ρ=1.06 g/cm³
- n_atoms=1.19×10²³ atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (+3 reaccions D + C ja existent) |
| material.py | ✅ mat_id=10 |
| build_dataset | ✅ 47.3M steps |
| train (30 epochs) | ✅ val_loss=0.25 |
| compare | ✅ (amb max-steps=50) |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Métriques por energía

| Energía | edep_W1 | Δd_W1 | cosθ_W1 | hits_ratio |
|---------|---------|-------|---------|------------|
| 0.025eV | 0.00000 | 0.010 | 0.017 | 0.94 |
| 1eV | 0.00000 | 0.010 | 0.017 | 0.98 |
| 100eV | 0.00000 | 0.016 | 0.019 | 1.05 |
| 10keV | 0.00001 | 0.010 | 0.016 | 1.08 |
| 100keV | 0.00021 | 0.014 | 0.015 | 1.12 |
| 1MeV | 0.00039 | 0.026 | 0.014 | 1.18 |
| 6MeV | 0.00057 | 0.137 | 0.029 | 1.75 |
| 14MeV | 0.00041 | 0.518 | 0.078 | 2.32 |

## Análisis
- **Hits ratio**: Excel·lent (0.94-1.18) per energies < 1 MeV
- **cosθ**: Molt bo (W1 < 0.02) a energies < 1 MeV
- **Δd**: Bo (W1 < 0.016) a energies < 100 keV
- **edep**: Molt baix → ratio no informativa

## Comparació amb PE
DPE és químicament similar a PE però amb deuteri. El deuteri té:
- Secció de captura més baixa → menys captures
- Secció elàstica similar → mateixa cinemàtica de scattering

Els resultats de DPE són **millors que D2O** i comparables a PE/Paraffin. Això és perquè DPE té densitat similar a PE (1.06 vs 0.94 g/cm³) i composició similar (CH₂ vs CD₂).

## Directorio de salida
`docs/site/nto/runs/nto_v110_DPE/seed1/`
