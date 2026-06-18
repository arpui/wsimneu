# H2O V11.0 — Conclusiones

**Data:** 2026-06-18  
**Modelo:** NTO V11.0  
**Material:** H2O (Aigua lleugera)  
**Training:** 12 epochs (timeout), val_loss=0.89

## Composición
- H: 66.7%, O: 33.3%
- Z_eff=3.33, A_eff=6.01, ρ=1.0 g/cm³
- n_atoms=1.00×10²³ atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (H, O ja al catàleg) |
| material.py | ✅ mat_id=5 |
| build_dataset | ✅ 122.8M steps |
| train (12 epochs) | ✅ val_loss=0.89 |
| compare | ✅ (max-steps=50) |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Notes
- Entrenament interromput per timeout (12/30 epochs)
- Dataset gran (122.8M steps) → training lent
- H2O és químicament similar a D2O però amb hidrogen regular
- Cal re-entrenar amb més epochs per millorar resultats

## Directorio de salida
`docs/site/nto/runs/nto_v110_H2O/seed1/`
