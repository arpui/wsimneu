# AirHumid V11.0 — Conclusiones

**Data:** 2026-06-17  
**Modelo:** NTO V11.0  
**Material:** AirHumid (N 76.9%, O 21.4%, H 1.4%, Ar 0.3%)  
**Training:** 30 epochs, val_loss=4.67

## Composición
- N: 66.75%, O: 16.27%, H: 16.89%, Ar: 0.09%
- Z_eff=6.16, A_eff=12.16, ρ=1.22×10⁻³ g/cm³
- n_atoms=6.04×10¹⁹ atoms/cm³

## Pipeline complet
| Paso | Estado |
|------|--------|
| reaction_catalog | ✅ (N, H ja + O, Ar nous) |
| material.py | ✅ mat_id=6 |
| build_dataset | ✅ 167K steps (MOLT POCS) |
| train (30 epochs) | ✅ val_loss=4.67 |
| compare | ✅ |
| one_step | ✅ |
| reaction_fractions | ✅ |

## Métriques por energía

| Energía | edep_W1 | edep_ratio | Δd_W1 | cosθ_W1 | hits_ratio |
|---------|---------|------------|-------|---------|------------|
| 0.025eV | 2.683 | 4.55e9 | 13.63 | 0.646 | 6.50 |
| 1eV | 2.030 | 5.41e8 | 11.45 | 0.686 | 13.31 |
| 100eV | 1.989 | 6.06e6 | 10.81 | 0.703 | 22.92 |
| 10keV | 2.106 | 7.35e4 | 10.71 | 0.705 | 29.88 |
| 100keV | 2.164 | 2.08e4 | 10.81 | 0.704 | 32.84 |
| 1MeV | 2.554 | 1.37e5 | 11.87 | 0.711 | 18.29 |
| 14MeV | 2.287 | 8.89e5 | 14.41 | 0.567 | 3.54 |

## ⚠️ Problemas detectados

1. **Dataset extremadamente petit**: 167K steps (vs 36M de Steel). L'aire té densitat molt baixa → pocs neutrons interactuen.
2. **hits_ratio molt alt (6-33x)**: El model genera masses hits perquè no aprèn bé la dinàmica de l'aire.
3. **Totes les mètriques són dolentes**: W1 > 1.0, ratios enormes.
4. **edep_ratio extrem**: Milers de milions — l'edep al truth és gairebé zero.

## Causa arrel
L'aire té densitat 1.000 vegades menor que PE. Amb 1M events, només ~167K steps són vàlids (la majoria de neutrons travessen l'aire sense interactuar). El dataset és insuficient per entrenar.

## Solucions proposades
1. **Més events**: 100M events en lloc de 1M per tenir ~16M steps
2. **Geometria més gran**: Augmentar la mida de la esfera d'aire
3. **Font més propera**: Posar la font dins de l'aire en lloc de al centre

## Directorio de salida
`docs/site/nto/runs/nto_v110_AirHumid/seed1/`
