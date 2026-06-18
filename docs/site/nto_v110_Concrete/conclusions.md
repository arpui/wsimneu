# Concrete V11.0 — Conclusiones

**Data:** 2026-06-18  
**Modelo:** NTO V11.0  
**Material:** Concrete (G4_CONCRETE NIST)  
**Training:** 30 epochs, val_loss=0.32

## Composición
- O: 45.5%, Si: 31.4%, Ca: 5.0%, Al: 4.6%, H: 1.0%, Fe: 0.9%, Na: 1.4%, K: 1.8%, C: 0.2%
- Z_eff=8.56, A_eff=17.03, ρ=2.3 g/cm³
- n_atoms=8.13×10²² atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (O, Si, H, C, N ja al catàleg) |
| material.py | ✅ mat_id=11 |
| build_dataset | ✅ 66.8M steps |
| train (30 epochs) | ✅ val_loss=0.32 |
| compare | ✅ (max-steps=50) |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Notes
- Concrete és un material complex amb molts elements
- Les reaccions principals són O i Si (80% del material)
- Elements minoritaris (Ca, Al, Fe) no tenen reaccions al catàleg → UNKNOWN
- Resultats raonables per a un material complex

## Directorio de salida
`docs/site/nto/runs/nto_v110_Concrete/seed1/`
