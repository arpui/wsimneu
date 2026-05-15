# Runbook — Sampling i avaluació de neutron_cascade

**Tags**: #runbook #operations #sampling #eval #neutron_cascade
**Last updated**: 2026-05-14

## Flux ràpid (recomanat)

```bash
# 1. Sampling amb config
./scripts/sample_run.sh runs/nc_multiE/run_NNN

# 2. Diagnosi amb truth
./scripts/diagnose_run.sh runs/nc_multiE/run_NNN

# 3. Pull al PC
./scripts/pull_neutron_cascade.sh

# 4. Comparació quantitativa
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --markdown docs/vault/results/figures/compare_007_010.md

# 5. Comparació visual
python scripts/compare_runs_figures.py \
    --fig-dirs docs/vault/results/figures/diagnose_run_007 \
               docs/vault/results/figures/diagnose_run_010 \
    --run-dirs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --layout rows --shared-parts A B \
    --out docs/vault/results/figures/compare_007_010 \
    --pdf
```

---

## Pre-requisits

- Training completat: ha d'existir `ckpt_final.pt` al directori del run.
- Fitxer preprocessat `.h5` disponible (el mateix usat per a training).
- venv activat (`actgenai` a ailab).

---

## Step 1: Sampling amb run_config.json

```bash
# Default: 5000 mostres × 7 energies definides al config
./scripts/sample_run.sh runs/nc_multiE/run_NNN

# Overrides
./scripts/sample_run.sh runs/nc_multiE/run_NNN --n-samples 10000 --n-steps 100

# Dry-run (només mostra la comanda)
DRY_RUN=1 ./scripts/sample_run.sh runs/nc_multiE/run_NNN
```

**Rerefons**: `sample_run.sh` → `_run_from_config.py` → `sample_neutron_cascade.py`.
Llegeix energies, n_samples, n_steps i feature_scale de `run_config.json`.

**Sortida per energia**:
```
runs/nc_multiE/run_NNN/samples_<E>/
├── samples.h5        # dades generades (hits + metadata)
├── stats.json        # estadístiques truth vs generated
└── figs/
    ├── marginal_r.png
    ├── marginal_z.png
    ├── marginal_t.png
    ├── marginal_edep.png
    ├── n_hits_dist.png
    ├── edep_2d_truth.png
    └── edep_2d_generated.png
```

**Temps**: ~3-5 min/energia × 7 energies ≈ 20-35 min total.

---

## Step 2: Stats.json — lectura ràpida

```bash
python -c "
import json
with open('runs/nc_multiE/run_NNN/samples_5MeV/stats.json') as f:
    s = json.load(f)
print('edep_z_bias_cm:', s.get('edep_z_centroid', {}).get('bias_cm'))
print('z_mean_bias_cm:', s.get('z', {}).get('mean_bias_cm'))
print('nhits_ratio:   ', s.get('n_hits', {}).get('mean_ratio'))
print('peak_r0_ratio: ', s.get('peak_r0_ratio'))
"
```

**Camps del stats.json**:
| Camp | Descripció |
|------|-----------|
| `edep_z_centroid.bias_cm` | Centroide z ponderat per edep (gen − truth) |
| `peak_r0_ratio` | Fracció hits r<0.2 cm gen/truth |
| `z.mean_bias_cm` | Desplaçament de la mitjana z (gen − truth) |
| `n_hits.mean_ratio` | Ratio n_hits generat/truth |

---

## Step 3: Criteris pass/fail

| Criteri | Pass | Warn | Fail |
|---------|------|------|------|
| edep_z_bias_cm | \|·\| < 2 cm | 2–4 cm | > 4 cm |
| z_mean_bias_cm | \|·\| < 1 cm | 1–2 cm | > 2 cm |
| nhits_ratio | 0.90–1.10 | 0.80–1.20 | fora |
| peak_r0_ratio | 0.80–1.25 | 0.70–1.40 | fora |
| Central pic (visual) | Visible a edep_2d | Feble | Absent |
| Support bounds | r ≥ 0, \|z\| ≤ 30 | — | r < 0 o z fora |

---

## Step 4: Pull al PC

```bash
# Default: figures + stats.json + configs
./scripts/pull_neutron_cascade.sh

# Només figures
./scripts/pull_neutron_cascade.sh --only-figs

# Amb checkpoint
./scripts/pull_neutron_cascade.sh --with-ckpts
```

---

## Diagnosi visual (ailab — amb truth HDF5)

```bash
# Genera figures amb truth overlay
./scripts/diagnose_run.sh runs/nc_multiE/run_NNN
```

**Figures C/D/E amb truth**:
- `C_z_phys.png` — histograma z físic: **blau = truth**, **taronja = generated**
- `D_edep_vs_z_scatter.png` — scatter edep vs z (truth + generated)
- `E_edep_z_profile.png` — perfil edep-weighted de z

**Nota**: Si s'executa sense `--truth`, les figures només mostren generats.

---

## Comparació quantitativa entre runs (PC)

```bash
# Taula per energia
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_001 runs/nc_multiE/run_002 \
    --labels "run_001 (baseline)" "run_002 (fs=5)" \
    --markdown docs/vault/results/compare_runs_001_002.md
```

Exporta a markdown amb ✅/WRN/BAD per cada mètrica i energia.

---

## Comparació visual (PDF)

```bash
# PDF amb totes les figures comparades
python scripts/compare_runs_figures.py \
    --fig-dirs docs/vault/results/figures/diagnose_run_001 \
               docs/vault/results/figures/diagnose_run_002 \
    --run-dirs runs/nc_multiE/run_001 runs/nc_multiE/run_002 \
    --labels "run_001" "run_002" \
    --layout rows --shared-parts A B \
    --out docs/vault/results/figures/compare_001_002 \
    --pdf
```

---

## Workflow complet — ordre d'operacions

```
ailab:
  1. Training:    ./scripts/train_run.sh runs/nc_multiE/run_NNN
  2. Sampling:    ./scripts/sample_run.sh runs/nc_multiE/run_NNN
  3. Diagnosi:    ./scripts/diagnose_run.sh runs/nc_multiE/run_NNN

PC:
  4. Pull:        ./scripts/pull_neutron_cascade.sh
  5. Compare Q:   python scripts/compare_runs_multiE.py --runs ... --markdown out.md
  6. Compare V:   python scripts/compare_runs_figures.py --fig-dirs ... --pdf
  7. Decidir:     run_N+1
```

---

## Troubleshooting

### Sample falla amb "E_in requereix --E-in"
El dataset és v3 (condZ). El script `sample_run.sh` passa `--E-in-eV` automàticament. Si es fa manualment, cal afegir-ho.

### Stats.json no es genera
Verifica que `sample_neutron_cascade.py` importa correctament `inverse_transform_continuous` des de `cfm.py`.

### edep_2d no es genera
```bash
export MPLBACKEND=Agg
```

### compare_runs_multiE mostra "—" per totes les mètriques
El stats.json no té els camps enriquits. Només els runs mostrejats amb `sample_neutron_cascade.py` post 2026-05-10 els tenen.

---

## Refs

- [[launch_training]] — com llançar el training previ
- [[diagnose_failures]] — si alguna cosa falla
- [[compare_energies]] — taula multi-energia MS3
- [[../milestones/MS1.x_decision_tree]] — arbre de decisions
- [[../results/discrepancies_tracker]] — D7/D8: bias sistemàtic i causes
- [[../concepts/inverse_transform]] — transformacions de normalització
