# D2O V11.0 — Conclusiones

**Data:** 2026-06-17  
**Modelo:** NTO V11.0  
**Material:** D2O (Aigua pesant: ²H₂O)  
**Training:** 30 epochs, val_loss=-0.47

## Composición
- D (²H): 66.7%, O: 33.3%
- Z_eff=3.33, A_eff=6.67, ρ=1.104 g/cm³
- n_atoms=9.96×10²² atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (+3 reaccions D: elastic, 2n, capture) |
| material.py | ✅ mat_id=9 |
| build_dataset | ✅ 29.2M steps |
| train (30 epochs) | ✅ val_loss=-0.47 |
| compare | ✅ (amb max-steps=50) |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Métriques por energía

| Energía | edep_W1 | Δd_W1 | cosθ_W1 | hits_ratio |
|---------|---------|-------|---------|------------|
| 0.025eV | 0.00000 | 0.043 | 0.030 | 1.47 |
| 1eV | 0.00000 | 0.087 | 0.033 | 1.59 |
| 100eV | 0.00000 | 0.131 | 0.037 | 1.71 |
| 10keV | 0.00015 | 0.153 | 0.041 | 1.77 |
| 100keV | 0.00168 | 0.133 | 0.036 | 1.82 |
| 1MeV | 0.00202 | 0.112 | 0.033 | 1.81 |
| 6MeV | 0.00179 | 0.437 | 0.059 | 2.87 |
| 14MeV | 0.00142 | 1.373 | 0.172 | 4.10 |

## Análisis
- **Hits ratio**: ~1.5-1.8 a energies baixes, degrada a 4.1 a 14 MeV
- **cosθ**: Acceptable (0.03-0.04) a energies < 1 MeV
- **Δd**: Degrada amb energia — 14 MeV té W1=1.37
- **edep**: Molt baix (gairebé zero) → ratio no informativa

## Comparació amb H2O
D2O és químicament similar a H2O però amb deuteri en lloc d'hidrogen. El deuteri té secció de captura més baixa que l'hidrogen → menys captures → més steps per event. Els resultats són raonables però no tan bons com PE/Paraffin.

## Directorio de salida
`docs/site/nto/runs/nto_v110_D2O/seed1/`
