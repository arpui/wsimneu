**Data:** 2026-06-18

# docs/site/nto/runs/nto_v110_D2O/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_D2O/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_D2O/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/D2O/truth/neutron_cascade_1.0MeV_20260617_074510.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 2.215e-08 | 2.556 | 1.177e-09 | 1.131 | 0.05216 | 0.01426 | nan | 1.469 |
| **0.1eV** | 1.502e-08 | 1.176 | 5.521e-10 | 1.052 | 0.1068 | 0.02181 | nan | 1.504 |
| **100eV** | 1.365e-06 | 0.619 | 1.253e-06 | 0.604 | 0.09511 | 0.03624 | nan | 1.726 |
| **100keV** | 0.001225 | 0.576 | 0.001523 | 0.558 | 0.1303 | 0.02884 | nan | 1.858 |
| **10eV** | 1.153e-07 | 0.788 | 1.148e-07 | 0.640 | 0.1186 | 0.02222 | nan | 1.659 |
| **10keV** | 0.000147 | 0.580 | 0.0001338 | 0.578 | 0.1187 | 0.04153 | nan | 1.801 |
| **14MeV** | 0.001117 | 0.794 | 0.896 | 0.423 | 1.333 | 0.17 | nan | 3.932 |
| **1MeV** | 0.001592 | 0.635 | 0.01843 | 0.703 | 0.1144 | 0.02651 | nan | 1.856 |
| **1eV** | 7.59e-09 | 1.301 | 8.053e-09 | 0.766 | 0.09859 | 0.02325 | nan | 1.586 |
| **1keV** | 1.355e-05 | 0.592 | 1.349e-05 | 0.589 | 0.1261 | 0.0376 | nan | 1.767 |
| **6MeV** | 0.001506 | 0.700 | 0.1458 | 0.471 | 0.4487 | 0.06168 | nan | 2.918 |

## Comparativa per energia (resum visual)

Cada figura conté 6 subplots de les variables natives del model: edep, ΔE_kin, Δd, cos(θ), Δt, N_hits/event.

### 0.025eV

![0.025eV](compare_0.025eV.png)

### 0.1eV

![0.1eV](compare_0.1eV.png)

### 100eV

![100eV](compare_100eV.png)

### 100keV

![100keV](compare_100keV.png)

### 10eV

![10eV](compare_10eV.png)

### 10keV

![10keV](compare_10keV.png)

### 14MeV

![14MeV](compare_14MeV.png)

### 1MeV

![1MeV](compare_1MeV.png)

### 1eV

![1eV](compare_1eV.png)

### 1keV

![1keV](compare_1keV.png)

### 6MeV

![6MeV](compare_6MeV.png)

## Gràfics detallats per mètrica

### edep

![grid edep](grid_edep.png)

### delta_E_kin

![grid delta_E_kin](grid_delta_E_kin.png)

### delta_r

![grid delta_r](grid_delta_r.png)

### delta_z

![grid delta_z](grid_delta_z.png)

### delta_d

![grid delta_d](grid_delta_d.png)

### cos_theta

![grid cos_theta](grid_cos_theta.png)

### delta_t

![grid delta_t](grid_delta_t.png)

### n_hits_per_event

![grid n_hits_per_event](grid_n_hits_per_event.png)

## Anàlisi

*(Secció per a anàlisi manual.)*

---

*Generat el 2026-06-18 per `scripts/compare_nto_to_truth.py`.*
