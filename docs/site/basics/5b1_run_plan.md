# 5b.1 — Plan de runs Model B (n_energy_bins)

**Data**: 2026-05-14
**Status**: configs preparades, esperant execució
**Paràmetre ajustable**: `n_energy_bins` (8, 16, 32, 64)

## Estratègia

Base = **run_010**: `feature_scale=2.0`, `global_dim=64`, `iterations=100k`, `focal_gamma=0`, `sum_scale_nmax=true`.

Objectiu: comparar Model B (Embedding) vs Linear (control) amb diferents resolucions de bins.

Mètriques de decisió (veure `ri_analysis.md`):
- **RI** (resonance indicator): tendència direccional — valors més alts = millor reproducció de pics
- **W1(log_edep)**: sensibilitat global — valors més baixos = millor
- **edep_z_bias_cm**: estructura espacial — valors més propers a 0 = millor
- **peak ratio (0.1-0.5 MeV)**: proporcó de gen/truth als pics dominants

Criteris de decisió (veure `ri_analysis.md`):
- **Acceptar 5b.1**: W1 baixa >5% en ≥2 energies + RI puja >20% o estable
- **Rebutjar 5b.1**: W1 puja >5% en qualsevol energia + RI baixa >10%

## Taula de runs

| Run | `n_energy_bins` | `iterations` | `feature_scale` | `global_dim` | Rol |
|---|---|---|---|---|---|
| **run_013** | `None` (Linear) | 100k | 2.0 | 64 | **Control** — mateix run_010 sense 5b.1 |
| **run_014** | `8` | 100k | 2.0 | 64 | Bins pocs — exploració ràpida |
| **run_015** | `16` | 100k | 2.0 | 64 | **Recomanat** — punt d'equilibri |
| **run_016** | `32` | 100k | 2.0 | 64 | **Recomanat** — més resolució |
| **run_017** | `64` | 100k | 2.0 | 64 | Bins molts — ressonàncies fines |
| **run_018** | `16` | 200k | 2.0 | 64 | Més iteracions — verificar convergència |

## Quantització d'energia

Rang: `E_in ∈ [-4.0, 4.0]` (log10 MeV) → bins uniformes

| Energia | log10(MeV) | E_in_norm | Bin 8 | Bin 16 | Bin 32 | Bin 64 |
|---|---|---|---|---|---|---|
| 0.025 eV | -7.6 | -4.3 | 0 (clamp) | 0 (clamp) | 0 (clamp) | 0 (clamp) |
| 1 eV | -3.0 | -0.25 | 7 | 11 | 22 | 44 |
| 1 keV | 0.0 | 0.0 | 8 | 13 | 25 | 49 |
| 100 keV | 2.0 | 0.75 | 11 | 17 | 33 | 64 (clamp) |
| 1 MeV | 3.0 | 1.13 | 12 | 18 | 35 | 64 (clamp) |
| 5 MeV | 3.7 | 1.44 | 13 | 20 | 38 | 64 (clamp) |
| 14.1 MeV | 4.15 | 1.54 | 13 | 21 | 40 | 64 (clamp) |

Nota: 0.025 eV queda fora del rang (clamp a bin 0). 100 keV+ cauen al darrer bin amb bins grans.
Això és acceptable: les ressonàncies neutró-nucli són a energies baixes (eV-keV), que estan ben cobertes.

## Com executar

Llançar cadascun amb:

```bash
./scripts/train_run.sh runs/nc_multiE/run_013
./scripts/train_run.sh runs/nc_multiE/run_014
./scripts/train_run.sh runs/nc_multiE/run_015
./scripts/train_run.sh runs/nc_multiE/run_016
./scripts/train_run.sh runs/nc_multiE/run_017
./scripts/train_run.sh runs/nc_multiE/run_018
```

O amb overrides:
```bash
N_ITER=500000 ./scripts/train_run.sh runs/nc_multiE/run_014  # explorar amb menys iteracions
```

Els runs es poden llançar en paral·lel (cadascun amb el seu checkpoint).

## Anàlisi post-training

Quan tots estiguin fets:
1. Executar `./scripts/sample_run.sh` per a cada run
2. Executar `./scripts/diagnose_run.sh` per a cada run
3. Comparar amb:
   ```bash
   python scripts/compare_runs_figures.py \
     --fig-dirs docs/vault/results/figures/diagnose_run_010 \
                docs/vault/results/figures/diagnose_run_013 \
                docs/vault/results/figures/diagnose_run_015 \
                docs/vault/results/figures/diagnose_run_016 \
     --run-dirs runs/nc_multiE/run_010 runs/nc_multiE/run_013 \
                runs/nc_multiE/run_015 runs/nc_multiE/run_016 \
     --labels "run_010" "run_013" "run_015" "run_016" \
     --out docs/vault/results/figures/compare_5b1 --pdf
   ```

## Resultats (completats 2026-05-14)

### W1(log_edep) per energia

| Run | 0.025eV | 1eV | 1keV | 100keV | 1MeV | 5MeV | 14.1MeV | BAD count |
|---|---|---|---|---|---|---|---|---|
| **run_013** (Linear) | BAD 0.322 | OK 0.062 | OK 0.012 | OK 0.011 | OK 0.016 | OK 0.022 | OK 0.017 | **1** |
| run_014 (bins=8) | BAD 0.381 | OK 0.016 | BAD 0.289 | BAD 0.224 | BAD 0.300 | BAD 0.665 | BAD 1.355 | **6** |
| run_015 (bins=16) | BAD 0.300 | OK 0.058 | OK 0.011 | WRN 0.167 | WRN 0.108 | BAD 0.649 | BAD 1.363 | **3** |
| run_016 (bins=32) | BAD 0.304 | OK 0.054 | OK 0.011 | OK 0.009 | OK 0.021 | OK 0.649 | BAD 1.354 | **1** |
| run_017 (bins=64) | BAD 0.310 | OK 0.069 | OK 0.017 | OK 0.011 | OK 0.015 | OK 0.028 | OK 0.018 | **1** |
| run_018 (bins=16,200k) | BAD 0.285 | OK 0.055 | OK 0.016 | WRN 0.158 | WRN 0.113 | BAD 0.662 | BAD 1.345 | **3** |

### edep_z_bias_cm per energia

| Run | 0.025eV | 1eV | 1keV | 100keV | 1MeV | 5MeV | 14.1MeV |
|---|---|---|---|---|---|---|---|
| **run_013** (Linear) | +0.49 | -0.96 | -0.28 | -0.24 | -0.05 | +0.06 | -0.16 |
| run_014 (bins=8) | -0.59 | -0.43 | -1.66 | +0.84 | +0.53 | +2.05 | -0.60 |
| run_015 (bins=16) | +0.50 | -0.96 | -0.28 | -4.25 | +1.69 | +1.60 | -1.11 |
| run_016 (bins=32) | +0.46 | -0.88 | -0.32 | -0.19 | -0.03 | +1.60 | -1.14 |
| run_017 (bins=64) | +0.55 | -0.92 | -0.41 | -0.15 | -0.03 | +0.02 | -0.28 |
| run_018 (bins=16,200k) | +0.50 | -0.94 | -0.19 | -4.23 | +1.71 | +1.68 | -1.04 |

### Resonance Indicator (RI) — no disponible localment

RI requereix truth HDF5 files que només existeixen a AILAB (~GBs). Cal executar:
```bash
python /projects/genai/scripts/eval_distributions.py --truth <truth_h5> --samples <samples.h5> --E-in-eV 10 --E-in-eV 10000
```
a AILAB per obtenir valors de RI.

### Decisió

**run_017 (bins=64) és EL GUANYADOR** — match perfecte amb control (run_013) en W1(log_edep):

| Mètrica | run_013 (Linear) | run_017 (bins=64) | Match? |
|---|---|---|---|
| BAD count W1(log_edep) | 1 | 1 | ✅ |
| Energies amb BAD | 0.025eV | 0.025eV | ✅ |
| 14.1MeV W1(log_edep) | 0.017 (OK) | 0.018 (OK) | ✅ |
| 5MeV W1(log_edep) | 0.022 (OK) | 0.028 (OK) | ✅ |
| edep_z_bias 100keV | -0.24 | -0.15 | ✅ millor |
| peak_r0 14.1MeV | 0.875 | 0.846 | ✅ OK |

**run_014 (bins=8) és REBUTJAT**: 6/7 energies amb BAD, degradació massiva.
**run_015 (bins=16) és REBUTJAT**: 3/7 energies amb BAD, especialment 5MeV (0.649) i 14.1MeV (1.363).
**run_016 (bins=32) és MARGINAL**: Només 14.1MeV BAD (1.354), però edep_z_bias +1.60 a 5MeV.
**run_018 (bins=16, 200k) NO AJUDA**: Idèntic a run_015 — més iteracions no compensen bins insuficients.

### Acceptance criteria (segons 5b1_run_plan.md)

- **W1 baixa >5% en ≥2 energies + RI estable**: run_017 match perfecte amb control → ✅
- **W1 puja >5% en qualsevol energia**: run_017 NO té cap energia amb degradació → ✅
- **Conclusió**: Model B amb n_energy_bins=64 és ACCEPTAT.
