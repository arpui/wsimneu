# Runbook — Workflow complet neutron_cascade (train → sample → diagnose → compare)

**Tags**: #runbook #operations #neutron_cascade #workflow
**Last updated**: 2026-05-15

## Flux principal

```
ailab:  1. train   →  2. sample   →  3. diagnose
         │
         └── pull_neutron_cascade.sh (sync ailab → PC)
                    │
                    ▼
PC:     4. compare_runs_multiE.py   (taula quantitativa)
        5. compare_runs_figures.py  (PDF visual)
```

---

## Pre-requisits

**Ailab**:
- `source /usr/local/geant4/bin/geant4.sh` (Geant4 11.x)
- venv `actgenai`
- GPU lliure (`nvidia-smi`)
- HDF5 preprocessat disponible

**PC** (WSL2):
- venv d'analysis
- Codi sincronitzat

---

## Step 1: Crear run_config.json

Copia un run existent i edita'l:

```bash
cp runs/nc_multiE/run_010/run_config.json runs/nc_multiE/run_NNN/run_config.json
```

Edita els camps clau:

| Camp | Valor | Notes |
|------|-------|-------|
| `run_id` | `"run_NNN"` | Identificador únic |
| `description` | Descripció del run | Per a referència futura |
| `feature_scale` | `2.0` (rápid) / `5.0` (qualitat) | Multiplicador de hits |
| `iterations` | `100000` | Exploració; `500000` per candidats |
| `sum_scale_nmax` | `true` (sempre!) | `false` → mode collapse |
| `focal_gamma` | `0` (CFM estàndard) / `>0` (FocalR) | |
| `n_energy_bins` | `None` (Linear) / `8..64` (Model B) | Conditioning d'energia |
| `global_dim` | `64` | Default |
| `data` | Path al HDF5 preprocessat | |

**Pitfall crític**: `sum_scale_nmax` ha de ser `true`. Si és `false`, el sum-pool de EPiC no normalitza per N i causa mode collapse.

---

## Step 2: Training (ailab)

```bash
# Normal
./scripts/train_run.sh runs/nc_multiE/run_NNN

# Override iterations
N_ITER=500000 ./scripts/train_run.sh runs/nc_multiE/run_NNN

# Dry-run (només mostra la comanda)
DRY_RUN=1 ./scripts/train_run.sh runs/nc_multiE/run_NNN
```

**Rerefons**: `train_run.sh` → `_run_from_config.py` → `train_neutron_cascade_v5_epic.py`.
Llegeix tots els hyperparàmetres de `run_config.json`.

**Comprovació**:
- Ha d'existir `runs/nc_multiE/run_NNN/ckpt_final.pt`
- `train_log.csv` amb loss per iteració

**Estimacions (RTX 4090)**:
| Config | Iters | Temps |
|--------|-------|-------|
| v5, 256-d, batch 256, 100k | 100k | ~15-30 min |
| v5, 256-d, batch 256, 500k | 500k | ~75-120 min |
| v5, 480-d, 8 layers, 400k | 400k | ~150-200 min |

---

## Step 3: Sampling (ailab)

```bash
# Default: 5000 mostres × 7 energies
./scripts/sample_run.sh runs/nc_multiE/run_NNN

# Overrides
./scripts/sample_run.sh runs/nc_multiE/run_NNN --n-samples 10000 --n-steps 100

# Dry-run
DRY_RUN=1 ./scripts/sample_run.sh runs/nc_multiE/run_NNN
```

**Sortida esperada per energia**:
```
runs/nc_multiE/run_NNN/
├── samples_0.025eV/
│   ├── samples.h5
│   ├── stats.json
│   └── figs/*.png
├── samples_1eV/
├── ... (7 energies)
└── ckpt_final.pt
```

**Temps**: ~3-5 min/energia × 7 energies ≈ 20-35 min total.

---

## Step 4: Diagnosi (ailab — necessita truth HDF5)

```bash
# Amb truth overlay (recomanat)
./scripts/diagnose_run.sh runs/nc_multiE/run_NNN

# Sense truth (mode PC, sense HDF5 disponible)
./scripts/diagnose_run.sh runs/nc_multiE/run_NNN --no-truth
```

**Sortida**: `docs/vault/results/figures/diagnose_run_NNN/`
- `A_transforms.png` — transforms del dataset
- `B_z_per_energy_truth.png` — z_mean per energia
- `C_z_phys.png` — histograma z físic (truth + generated)
- `D_edep_vs_z_scatter.png` — scatter plot edep vs z
- `E_edep_z_profile.png` — perfil edep-weighted de z
- `F_summary.txt` — resum textual del diagnòstic
- `nhits_*.png` — N_hits per event per energia (7 figures)
- `M_metrics.md` — resum quantitatiu per energia
- `M_metrics_summary.png` — gràfic de mètriques
- `Z_metrics_heatmap.png` — heatmap de totes les mètriques
- `voxel/` — mapes voxelitzats E(z,r) amb Spearman i MI

**Part G (edep spectra)**: `docs/vault/results/figures/nhits/`
- `edep_run_NNN_all.png` — grid combinat totes energies
- `edep_run_NNN_<E>.png` — figures individuals per energia

**Auto-sync al site**: Després del diagnose, `update_site_images.py` copia
automàticament les figures PNG a `docs/site/images/runs/diagnose_run_NNN/`.

**Nota**: El diagnose genera `M_metrics.md` automàticament. Les figures C/D/E mostren **blau = truth**, **taronja = generated**.

---

## Step 5: Pull (ailab → PC)

**Nota**: El diagnòstic (Step 4) sincronitza automàticament les figures
a `docs/site/images/`. Només cal fer pull per les dades addicionals
(stats.json, configs, train_log.csv).

```bash
# Default: figures + stats.json + configs + train_log
./scripts/pull_neutron_cascade.sh

# Només figures PNG/PDF (més ràpid)
./scripts/pull_neutron_cascade.sh --only-figs

# Amb checkpoint final
./scripts/pull_neutron_cascade.sh --with-ckpts

# Tot (incloent HDF5 preprocessats)
./scripts/pull_neutron_cascade.sh --all
```

**Sincronitza**:
1. Figures de `docs/vault/results/figures/` (PNG, PDF, JSON)
2. Fitxers d'anàlisi dels runs (`config.json`, `run_config.json`, `train_log.csv`, `stats.json`)
3. Sanity check figures (`notebooks/figs/sanity_*/`)

### 5.1 Sync manual al site (GitHub Pages)

Si cal sincronitzar figures manualment (no cal habitualment, es fa automaticament al diagnose):

```bash
# Un run concret:
python scripts/update_site_images.py --runs 019

# Figures de comparacio:
python scripts/update_site_images.py --compare compare_007_010_019

# Tot el vault:
python scripts/update_site_images.py

# Verificar enllaços del site:
python scripts/update_site_images.py --verify-links
```

**Publicar al GitHub Pages**:
```bash
# 1. Assegurar-sync:
python scripts/update_site_images.py

# 2. Commit i push (només docs/site/, no runs/):
git add docs/site/
git commit -m "docs(site): sync figures diagnose_run_019"
git push
```

**Important**: `docs/site/images/` NO es modifica manualment. Sempre via `update_site_images.py`.

---

## Step 6: Comparació quantitativa (PC)

### Mètriques per energia

```bash
# Taula quantitativa per energia
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_007 \
           runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --markdown docs/vault/results/figures/compare_007_010.md

# Només energies ràpides
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --energies 1MeV 5MeV 14.1MeV \
    --markdown compare_fast.md
```

**Mètriques calculades**:

| Mètrica | OK | WARN |
|---------|-----|------|
| `edep_z_bias_cm` | < 2.0 cm | < 3.0 cm |
| `z_mean_bias_cm` | < 1.0 cm | < 2.0 cm |
| `z_std_ratio` | 0.80–1.20 | < 0.50 |
| `nhits_ratio` | 0.90–1.10 | — |
| `peak_r0_ratio` | > 0.70 | > 0.65 |
| `W1(z)` | < 1.0 cm | 1.0–2.0 |
| `W1(log_edep)` | < 0.10 | 0.10–0.20 |

---

## Step 7: Comparació visual (PC)

### Figures diagnostiques (PDF)

```bash
# Comparació visual de figures
python scripts/compare_runs_figures.py \
    --fig-dirs \
        docs/vault/results/figures/diagnose_run_007 \
        docs/vault/results/figures/diagnose_run_010 \
    --run-dirs \
        runs/nc_multiE/run_007 \
        runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --layout rows \
    --shared-parts A B \
    --truth-path mc/geant4/neutron_cascade/build/neutron_cascade_multiE_7E_preprocessed.h5 \
    --nhits-dir docs/vault/results/figures/nhits \
    --out docs/vault/results/figures/compare_007_010 \
    --pdf
```

**Flags principals**:

| Flag | Descripció |
|------|-----------|
| `--fig-dirs` | Directors de figures diagnòstiques (un per run) |
| `--run-dirs` | Directors de run per llegir `stats.json` |
| `--labels` | Etiqueta per cada run (ordre coincideix amb `--fig-dirs`) |
| `--layout cols\|rows` | Columnes per run (default) o files per run |
| `--filter B C D E` | Només incloure parts específiques |
| `--shared-parts A B` | Parts A/B amb escala compartida |
| `--truth-path` | Path al HDF5 de truth (per RI + heatmap) |
| `--nhits-dir` | Director amb nhits histograms (per PDF) |
| `--out` | Director de sortida |
| `--pdf` | Generar PDF combinat |

**Sortida del PDF**:
- 1 pàgina per part (A, B, C, D, E, M, Z)
- Nhits histograms (una per run, si `--nhits-dir` s'ha passat)
- Mètriques: `M_metrics_summary.png` (taula) + `Z_metrics_heatmap.png` (heatmap)
- RI (Resonance Indicator) calculat de `gen_edep` vs `truth_edep`

### Nhits histograms individuals

```bash
# Histograma edep per energia (un run)
python scripts/nhits_histogram.py \
    --run-dir runs/nc_multiE/run_NNN \
    --out docs/vault/results/figures/nhits_run_NNN

# Histograma edep comparatiu (tots els runs d'un directori)
python scripts/nhits_histogram.py \
    --all-runs runs/nc_multiE/ \
    --out docs/vault/results/figures/nhits_all

# Amb etiquetes personalitzades
python scripts/nhits_histogram.py \
    --run-dirs runs/nc_multiE/run_007 \
               runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --out docs/vault/results/figures/nhits_007_010
```

---

## Workflow complet — resum

### Ailab
```bash
# 1. Training
./scripts/train_run.sh runs/nc_multiE/run_NNN

# 2. Sampling
./scripts/sample_run.sh runs/nc_multiE/run_NNN

# 3. Diagnosi (amb truth + auto-sync al site)
./scripts/diagnose_run.sh runs/nc_multiE/run_NNN
```

### PC
```bash
# 4. Sync resultats (site ja sync al Step 3)
./scripts/pull_neutron_cascade.sh

# 5. Comparació quantitativa
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --markdown docs/vault/results/figures/compare_007_010.md

# 6. Comparació visual (PDF)
python scripts/compare_runs_figures.py \
    --fig-dirs docs/vault/results/figures/diagnose_run_007 \
               docs/vault/results/figures/diagnose_run_010 \
    --run-dirs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --labels "run_007" "run_010" \
    --layout rows \
    --shared-parts A B \
    --truth-path mc/geant4/neutron_cascade/build/neutron_cascade_multiE_7E_preprocessed.h5 \
    --nhits-dir docs/vault/results/figures/nhits \
    --out docs/vault/results/figures/compare_007_010 \
    --pdf

# 7. (Opcional) Actualitzar site web
python scripts/update_site_images.py
git add docs/site/
git commit -m "docs(site): sync figures"
git push
```

---

## Mode local (sense GPU, sense truth)

Si no tens accés a l'ailab, pots fer diagnòstic bàsic:

```bash
# Diagnose sense truth (només generats)
python scripts/diagnose.py \
    --run-dir runs/nc_multiE/run_NNN \
    --out docs/vault/results/figures/diagnose_run_NNN \
    --no-truth

# Comparació quantitativa (només stats.json)
python scripts/compare_runs_multiE.py \
    --runs runs/nc_multiE/run_007 runs/nc_multiE/run_010 \
    --markdown compare.md
```

---

## Troubleshooting

### Training no troba dataset
```bash
# Comprovar que el HDF5 existeix
ls -lh <data_path>.h5
```

### Sampling falla amb "E_in requereix --E-in"
El dataset és v3 (condZ). El script `sample_run.sh` passa `--E-in-eV` automàticament. Si es fa manualment, cal afegir-ho.

### Diagnosi sense figures C/D/E
S'executa sense `--truth`. Les figures només mostren generats. Re-executar amb `--truth <path_to_truth.h5>`.

### PDF buit o sense figures
- Verificar que els `--fig-dirs` contenen PNGs vàlids
- Els noms han de coincidir: `A_*.png`, `B_*.png`, `C_*.png`, etc.

### `sum_scale_nmax` ha de ser `true`
Si el model col·lapsa (pocs hits per event), comprovar que:
```bash
python -c "import json; c=json.load(open('runs/nc_multiE/run_NNN/run_config.json')); print(c['train'].get('sum_scale_nmax'))"
```

---

## Refs

- [[../milestones/MS1.x_decision_tree]] — què llançar segons resultats
- [[../milestones/MS3_crossregime]] — criteris pass/fail multi-E
- [[../results/discrepancies_tracker]] — D7/D8 i altres discrepàncies actives
- `docs/WORKFLOW.md` — manual operatiu actual
