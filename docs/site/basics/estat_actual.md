# Estat actual del projecte — neutron_cascade

**Última actualització**: 2026-05-11 — **Sweep fs×condZ completat i diagnosticat (007/008). Guanyador: run_006 (fs=20). run_009 (500k) en curs.**

---

## On som

**Fase 1 completada (MS3 ✅ PASS).** Ara en fase d'iteració post-MS3:
explorant millores al model EPiC-FM per reduir el bias sistemàtic
identificat a `edep_z_centroid` (discrepàncies D7 i D8).

**Sweep fs×condZ completat** (run_004/005/006 → 100k cadascun):
- **run_001**: baseline 5E, fs=1, noZ → ✅ MS3 PASS (z_mean_bias ≈ −2 cm a 1MeV)
- **run_002**: 7E, fs=5, noZ → ✅ vàlid, z_mean_bias idèntic a run_001 (fs no ajuda)
- **run_003**: 7E, condZ + FocalR + sum_scale=False → ❌ col·lapse (experiment confundit)
- **run_004**: 7E, fs=5, condZ → ❌ sense samples — sospita de col·lapse (loss=2.93≈5×0.59)
- **run_005**: 7E, fs=1, condZ → ❌ col·lapse confirmat (z_std≪truth, Part E plana)
- **run_006**: 7E, fs=20, condZ → ✅ **guanyador** — Δ=0.0cm@1MeV, Δ=−0.3cm@5MeV, Δ=−1.1cm@14.1MeV

**Conclusió sweep**: condZ necessita fs≥20 per no col·lapsar. Loss escala linealment amb fs
(no comparable entre runs). Llindar exacte a determinar amb run_007/008.

**Proper pas**: run_009 (fs=20, condZ, 500k) — full run del guanyador.

---

## Discrepàncies actives

| ID | Descripció | Causa probable | Estat |
|----|------------|----------------|-------|
| **D7** | Cues curtes (z>0 cm) en generated per neutrons ràpids | z_global_mean=-18 cm → z>0 és zona extrema per al model | run_004 (condZ net) hauria de millorar |
| **D8** | Excés edep a entrada moderador (z≈-30 cm) | Model barreja distribucions de totes les energies | run_004 (condZ net) hauria de millorar |

Detall complet: [[results/discrepancies_tracker]]

**Impacte mesurat (run_001)**:

| Energia | edep_z_bias [cm] |
|---------|-----------------|
| 0.025eV | -0.73 |
| 1eV     | -0.58 |
| 1keV    | — (no mostrejat a run_001) |
| 100keV  | — (no mostrejat a run_001) |
| 1MeV    | -2.14 |
| 5MeV    | -2.54 |
| 14.1MeV | -2.48 |

---

## Taula de runs nc_multiE

| Run | Dataset | Energies | Feature-scale | Notes | Estat |
|-----|---------|----------|---------------|-------|-------|
| **run_001** | 7E v2 | 0.025eV,1eV,1MeV,5MeV,14.1MeV | 1.0 | Baseline, global_dim=64 | ✅ MS3 PASS |
| **run_002** | 7E v2 | 0.025eV–14.1MeV | 5.0 | fs=5, sense condZ | ✅ vàlid (Δ≈−1.5cm) |
| **run_003** | 7E v3 condZ | 0.025eV–14.1MeV | 5.0 | sum_scale=**False** + FocalR | ❌ col·lapse (experiment confundit) |
| **run_004** | 7E v3 condZ | 0.025eV–14.1MeV | 5.0 | condZ + fs=5 | ❓ sense samples (descartada, repetida per run_007) |
| **run_005** | 7E v3 condZ | 0.025eV–14.1MeV | 1.0 | condZ + fs=1 | ❌ col·lapse confirmat (z_std≪truth) |
| **run_006** | 7E v3 condZ | 0.025eV–14.1MeV | 20.0 | condZ + fs=20 | ✅ **guanyador sweep** — Δ=0cm@1MeV, perfils nets |
| **run_007** | 7E v3 condZ | 0.025eV–14.1MeV | 5.0 | redo run_004 — confirmar llindar col·lapse | ⚠️ NO col·lapsa, però perfils tèrmics sorollosos |
| **run_008** | 7E v3 condZ | 0.025eV–14.1MeV | 12.0 | llindar entre 5 i 20 | ⚠️ NO col·lapsa, qualitat similar a run_007 |
| **run_009** | 7E v3 condZ | 0.025eV–14.1MeV | 20.0 | **full run 500k guanyador** | 🔜 en curs (~4.5h) |

**Resultat sweep complet** (diagnosticat amb compare_runs.py):

| fs | run | Col·lapse | Qualitat perfils | Observació |
|----|-----|-----------|-----------------|-----------|
| 1  | 005 | ❌ SÍ     | —               | z_std≪truth, Part E plana |
| 5  | 007 | ✅ NO     | ⚠️ baixa        | Perfils tèrmics molt sorollosos |
| 12 | 008 | ✅ NO     | ⚠️ baixa        | Similar a fs=5, no millora apreciable |
| 20 | 006 | ✅ NO     | ✅ alta         | Perfils nets, Δ≈0cm@1MeV |

**Llindar de col·lapse**: entre **fs=1 i fs=5** (no entre 5 i 20 com sospitàvem).
**Gradient de qualitat**: fs=20 és significativament millor que fs=5/12 en perfils tèrmics.
**Conclusió**: run_009 (fs=20, 500k) és el camí correcte. fs=5/12 no col·lapsen però no arriben a la qualitat de fs=20.

---

## Taula de runs completa (historial)

| Run | Arquitectura | Iter | Loss | Characteristic | Estab. | Resultat |
|---|---|---|---|---|---|---|
| nc_5MeV run_001–004 | ViT v1–v3.1 | 200k | 0.19–0.28 | MSE/Weighted/partID | ✅ | ❌ central pic absent |
| nc_5MeV run_005 | ViT v2 | 200k | — | FocalR γ=1.0 | ✅ | ✅ MS1.2 pass |
| nc_5MeV run_006–007 | EPiC-FM | <200k | divergent | sense sum_scale | ❌ | ❌ spikes |
| **nc_5MeV run_008** | **EPiC-FM** | **500k** | **0.621** | **sum_scale=1/N** | **✅** | **✅ MS1 pass** |
| nc_5MeV run_009 | EPiC-FM | 500k | — | FocalR γ=0.5 | ✅ | col·lateral |
| **nc_multiE run_001** | **EPiC-FM** | **500k** | **—** | **global_dim=64, 5E** | **✅** | **✅ MS3 PASS** |
| **nc_multiE run_002** | **EPiC-FM** | **500k** | **—** | **7E, fs=5, sense condZ** | **✅** | **analitzant figures** |
| **nc_multiE run_003** | **EPiC-FM** | **500k** | **7.4 (plateau)** | **7E condZ, sum_scale=False, FocalR** | **❌** | **col·lapse — experiment confundit** |
| **nc_multiE run_004** | **EPiC-FM** | **100k** | **2.93** | **7E condZ, fs=5, MSE** | **❓** | **sense samples — probable col·lapse** |
| **nc_multiE run_005** | **EPiC-FM** | **100k** | **0.588** | **7E condZ, fs=1, MSE** | **❌** | **col·lapse confirmat (z_std≪truth)** |
| **nc_multiE run_006** | **EPiC-FM** | **100k** | **12.06** | **7E condZ, fs=20, MSE** | **✅** | **guanyador sweep — Δ=0cm@1MeV** |
| **nc_multiE run_007** | **EPiC-FM** | **100k** | **—** | **7E condZ, fs=5, MSE (redo 004)** | **🔜** | **confirmar col·lapse fs=5** |
| **nc_multiE run_008** | **EPiC-FM** | **100k** | **—** | **7E condZ, fs=12, MSE** | **🔜** | **llindar fs (condicional a 007)** |
| **nc_multiE run_009** | **EPiC-FM** | **500k** | **—** | **7E condZ, fs=20, MSE** | **🔜** | **full run guanyador — prioritat** |

---

## MS3 — Resultat final ✅ (run_001)

**run_001** (`runs/nc_multiE/run_001/`), EPiC-FM, 500k iter, 4h15m:

| Mètrica | 0.025eV | 1eV | 1MeV | 5MeV | 14.1MeV |
| --- | --- | --- | --- | --- | --- |
| peak_r0_ratio | ✅ 2.036 | ✅ 1.546 | ✅ 1.030 | ✅ 1.018 | ✅ 0.925 |
| z_peak_bias | ✅ -0.14 | ✅ -0.18 | ✅ -0.41 | ✅ +1.03 | ✅ +0.81 |
| edep_z_bias | ✅ -0.73 | ✅ -0.58 | ✅ -2.14 | ✅ -2.54 | ✅ -2.48 |
| nhits_mean_ratio | ✅ 1.091 | ✅ 0.979 | ✅ 1.015 | ✅ 0.961 | ✅ 1.008 |
| W1(r/z/t/edep) | ✅✅✅✅ | ✅✅✅✅ | ✅✅✅✅ | ✅✅✅✅ | ✅✅✅✅ |

Detall complet: [[results/nc_multiE_run_001]]

---

## Eines disponibles (estables)

### Scripts genèrics de training/sampling (a partir de run_004)

Cada run té un `run_config.json` a `runs/nc_multiE/<run>/` amb tots els paràmetres.
Els scripts llegeixen el config i construeixen la comanda automàticament.

```bash
# Training (iteracions del config, per defecte 100k)
./scripts/train_run.sh runs/nc_multiE/run_007

# Full run sense modificar el config
N_ITER=500000 ./scripts/train_run.sh runs/nc_multiE/run_007

# Sampling (totes les energies del config)
./scripts/sample_run.sh runs/nc_multiE/run_007

# Sampling amb més mostres
./scripts/sample_run.sh runs/nc_multiE/run_007 --n-samples 10000

# Verificar sense executar
DRY_RUN=1 ./scripts/train_run.sh runs/nc_multiE/run_007
```

Per crear un nou run: `cp -r runs/nc_multiE/run_template runs/nc_multiE/run_007`
i editar `run_id`, `description`, `feature_scale`, etc.

> **Git i sincronització**: `runs/` està al `.gitignore` (pesos i mostres no es versionen).
> Els `run_config.json` **sí** es commitegen (estan dins `runs/` però es poden afegir amb
> `git add -f runs/nc_multiE/run_007/run_config.json`).
> Per sincronitzar runs d'ailab al PC: `./scripts/pull_neutron_cascade.sh`
> Per pujar codi a ailab: `git push` al PC + `git pull` a ailab.

**Motor:**
- `scripts/_run_from_config.py` — lògica Python compartida train+sample
- `scripts/train_run.sh` / `scripts/sample_run.sh` — wrappers bash (1 línia cadascun)
- `runs/nc_multiE/run_template/run_config.json` — plantilla per a nous runs

**Scripts legacy (run_001/002/003, mantinguts per referència):**
- `scripts/train_run003.sh`, `scripts/sample_run002.sh`, `scripts/sample_run003.sh`

---

### Altres eines

**Training base:**
- `scripts/train_neutron_cascade_v5_epic.py` — training EPiC-FM mono/multi-E

**Preprocessament:**
- `scripts/preprocess_nc_v3_condz.py` — crea dataset v3 amb z normalització condicional en E
- `scripts/concat_neutron_hdf5.py` — concat HDF5 multi-E amb `--max-events-per-file`

**Avaluació / diagnosi:**
- `scripts/eval_distributions.py` — mètriques W1/KS/cascade amb `--E-in` filter
- `scripts/compare_energies.py` — matriu mètrica × energia, diagnosi MS3
- `scripts/compare_runs_multiE.py` — comparació quantitativa runs (stats.json per energia)
- `scripts/compare_runs_figures.py` — graella visual comparativa PNG entre runs (local, `--layout cols|rows`)
- `scripts/diagnose_z_bias.py` — diagnosi visual edep_z_bias per energia

**Infraestructura MC:**
- `mc/geant4/neutron_cascade/` — Geant4 amb G4ThermalNeutrons, macros 7 energies
- `scripts/pull_neutron_cascade.sh` — sync ailab → PC (runs + figures)

---

## Datasets disponibles a ailab

| Fitxer | Energies | Events | Notes |
|--------|----------|--------|-------|
| `neutron_cascade_multiE_1M_preprocessed.h5` | 5E (0.025eV–14.1MeV) | ~1M | v2, run_001 |
| `neutron_cascade_multiE_7E_preprocessed.h5` | 7E (+1keV,100keV) | ~1.4M | v2, run_002 |
| `neutron_cascade_multiE_7E_condz_preprocessed.h5` | 7E | ~1.4M | v3 condZ, run_003 |

## Truth HDF5 (PC)

Path absolut al fitxer de ground truth per a diagnòstic i figures amb overlay:

```
mc/geant4/neutron_cascade/build/neutron_cascade_multiE_7E_condz_preprocessed.h5
```

- Utilitzat per: `scripts/nhits_histogram.py --edep-per-energy`, `scripts/diagnose_run.sh --no-truth`, figures de comparació
- Conté: `/hits`, `/event_offsets`, `/n_hits`, `/E_in`, `/metadata` amb transforms inverse aplicades
- Utilitzat pels runs 007 i 010 per generar figures G (edep) i H (isoletàrgic) amb overlay de truth

---

## Criteris quantitatius per avaluar runs (post-MS3)

Mètriques extretes de `stats.json` (generat per `compare_runs_multiE.py` + `eval_distributions.py`).
Visualitzades a la Part M del compare PDF.

### Mètriques principals

| Mètrica | Descripció | Target PASS | Notes |
|---------|-----------|-------------|-------|
| **z_mean_bias_cm** | μ(z_gen) − μ(z_truth) per energia | `|Δ| < 1.0 cm` | Principal indicador D7/D8 |
| **edep_z_bias_cm** | centroide z ponderat edep: gen vs truth | `|Δ| < 1.5 cm` | Part E del diagnose |
| **nhits_ratio** | N_hits_gen / N_hits_truth | `0.85 – 1.15` | Multiplicicat de cascada |
| **peak_r0_ratio** | pic radial r≈0: gen/truth | `0.5 – 2.0` | Estructura central |
| **z_std_ratio** | σ(z_gen) / σ(z_truth) per energia | `0.8 – 1.2` | **Detector de col·lapse**: run_005 tenia ~0.15 |

### Per a run_009 (full 500k, fs=20 condZ) — objectiu

Basant-se en run_006 (100k, mateixa config):

| Energia | z_mean_bias target | edep_z_bias target |
|---------|-------------------|-------------------|
| 0.025eV–100keV | `|Δ| < 0.5 cm` | `|Δ| < 0.5 cm` |
| 1MeV    | **`|Δ| < 0.3 cm`** (run_006: 0.0 cm) | `|Δ| < 1.0 cm` |
| 5MeV    | **`|Δ| < 0.5 cm`** (run_006: 0.3 cm) | `|Δ| < 1.0 cm` |
| 14.1MeV | **`|Δ| < 1.0 cm`** (run_006: 1.1 cm) | `|Δ| < 1.5 cm` |

Si run_009 passa aquests criteris → **MS4 PASS** (surrogate condZ validat a 500k).

### Per a identificar col·lapse (sense figures)

```
z_std_ratio < 0.5  →  col·lapse probable
loss / fs     ≈ constant (0.59)  →  NO diagnòstic (vàlid per a totes les fs)
```

---

## Pendent

- [ ] **Revisar figures run_001 vs run_002** (fs=1 vs fs=5) — WIP ara
- [ ] **Llançar sweep fs × condZ** — run_004/005/006 a 100k cadascun:
  ```bash
  python scripts/train_neutron_cascade_v5_epic.py \
      --data .../neutron_cascade_multiE_7E_condz_preprocessed.h5 \
      --output runs/nc_multiE/run_004 \
      --feature-scale 5.0 --sum-scale-nmax --focal-gamma 0.0 \
      --iterations 100000 --global-dim 64
  ```
- [ ] Analitzar sweep: `compare_runs_multiE.py` amb run_001/002/004/005/006 → quin fs és millor amb condZ?
- [ ] Full run 500k del millor fs
- [ ] Diagnosi visual run_002 a ailab (D7/D8 confirmats sense condZ)
- [ ] Documentar run_003 (col·lapse) i run_004 al vault

## Conceptes clau: feature_scale vs condZ

**feature_scale** (fs): multiplica TOTS els inputs per un escalar. Efecte: rangs inputs més grans.
- fs=1 (run_001) vs fs=5 (run_002): z_mean_bias pràcticament idèntic → **fs no redueix el bias**.
- Útil per estabilitat de gradient, però no ataca el mismatch de distribució z × energia.

**condZ**: normalitza z de forma energy-dependent: `z_norm = (z − z_mean_poly(E)) / z_std_global`
- Cada energia té la seva distribució z centrada a 0 → el model no barreja energies.
- D7 (z>0 per ràpids): amb condZ, z>0 deixa de ser un valor extrem → model ha de generar-ho.
- D8 (excés z≈−30 cm): condZ separa tèrmics de ràpids → model aprèn cada una per separat.
- **Hipòtesi**: run_004 (condZ net, sense errors) hauria de reduir z_mean_bias significativament.

---

## Estratègia dual-track (post-MS3)

Dos objectius paral·lels amb naturaleses i expectatives diferents:

### Track 1 — Millora del surrogate actual
**Branca**: `main` / `track1/epic-improvements`
**Objectiu**: reduir z_mean_bias i resoldre D7/D8. Mètriques clares, timeline definit.
**Pròxims passos**: sweep fs×condZ (run_004/005/006), log-transform edep, procID head aux.

### Track 2 — Discrete FM + procID joint generation
**Branca**: `track2/discrete-fm` (pendent de crear)
**Objectiu**: simulador físicament complet que genera procID conjuntament amb (r,z,t,edep).
**Naturalesa**: recerca oberta. No té mètriques PASS/FAIL com Track 1. Pot no arribar a
milestone concret però l'aprenentatge físic és vàlid en si mateix.
**Prerequisit**: analitzar distribució de procID al truth (Fase 0 de l'ADR-009).
**No bloca Track 1.**

Detall: [[decisions/ADR_009_discrete_fm_track2]]

---

## Fase 2 — Per definir

Objectiu: benchmark inter-eines sobre cas neutron-PE comú.

Eines a integrar: Geant4, OpenMC, FLUKA, GATE 10.
Energia/geometria de referència: font AmBe o fissió Watt + moderador PE.
Surrogate generatiu: model multi-E de Fase 1 com a baseline.

Detall: [[milestones/MS3_crossregime]] + CLAUDE.md §Fase 2.

---

*Fitxer viu — actualitzar quan canviï l'estat del projecte.*

## Protocol d'iteració ràpida (quick-run)

Basat en l'anàlisi de convergència de runs anteriors:
- run_001: 99% millora a ~104k iter (30.7 ms/step, MSE)
- run_002: 99% millora a ~48k iter (32.0 ms/step, MSE)
- run_003: plateau des de 25k (63.9 ms/step, FocalR — 2× més lent)

**Regla**: per qualsevol nou run de test de paràmetres, fer **primer 100k iter (~54 min amb MSE)**.
- Si loss < 1.0 i decreixent → convergència OK → llançar 500k (~4.5h)
- Si loss > 5 o plateau des de l'inici → col·lapse → investigar config abans de seguir
- FocalR **no usar** en runs ràpids de diagnosi (dobla el temps, confon l'experiment)
