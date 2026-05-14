# 🏠 Neutron Cascade — Experiment Tracker

Tracking de runs d'entrenament del model neutron cascade amb condicionar per energia.

## Phase 5b.1 — Model B (`n_energy_bins`) vs Linear control

| Run | Model | n_bins | Iteracions | feature_scale | Status |
|-----|-------|--------|------------|---------------|--------|
| [run_013](../runs/run_013.md) | Linear (control) | — | 100k | 2.0 | ✅ Completat |
| [run_014](../runs/run_014.md) | Model B | 8 | 100k | 2.0 | ✅ Completat |
| [run_015](../runs/run_015.md) | Model B | 16 | 100k | 2.0 | ✅ Completat |
| [run_016](../runs/run_016.md) | Model B | 32 | 100k | 2.0 | ✅ Completat |
| [run_017](../runs/run_017.md) | Model B | 64 | 100k | 2.0 | ✅ Completat |
| [run_018](../runs/run_018.md) | Model B | 16 | 500k | 2.0 | ✅ Completat |

## Veredicte

**Model B amb `n_energy_bins` NO supera el Linear embedding.**

- **bins=64** (run_017) ≈ Linear en TOTES les mètriques
- **bins=8/16** degraduen a energies mitjanes (5MeV, 14.1MeV) amb W1_z > 2.0
- **bins=32** és intermedi, millor que 8/16 però pitjor que 64
- **200k iteracions** (run_018) no milloren el resultat → problema arquitectural

📊 [Veure comparativa completa](compare/compare_5b1.md)

## Runs anteriors

| Run | Notes |
|-----|-------|
| run_001–012 | Runs anteriors de calibratge i condZ |
| run_010 | Referència: feature_scale=2.0, 500k iteracions |

## Com funciona aquesta pàgina

Cada run té la seva pàgina amb:
- Motivació i paràmetres de configuració
- Mètriques per energia (W1, bias, peak_r0)
- Gràfics A–F (transforms, z_phys, scatter, perfils)
- Links a runs comparats

Els gràfics es serveixen directament des del repo via GitHub.
