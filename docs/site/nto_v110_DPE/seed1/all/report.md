**Data:** 2026-06-17

# docs/site/nto/runs/nto_v110_DPE/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_DPE/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_DPE/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/DPE/truth/neutron_cascade_1.0MeV_20260617_114921.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 3.911e-09 | 1.072 | 6.53e-10 | 1.060 | 0.01019 | 0.01677 | nan | 0.936 |
| **0.1eV** | 3.928e-09 | 1.093 | 6.408e-10 | 1.072 | 0.02101 | 0.02359 | nan | 0.946 |
| **100eV** | 1.035e-06 | 2.121 | 1.079e-07 | 0.974 | 0.01584 | 0.01916 | nan | 1.049 |
| **100keV** | 0.0002114 | 0.928 | 0.0001131 | 0.915 | 0.01357 | 0.0151 | nan | 1.116 |
| **10eV** | 7.053e-09 | 1.018 | 7.524e-09 | 1.004 | 0.01688 | 0.01517 | nan | 1.018 |
| **10keV** | 1.323e-05 | 0.954 | 1.448e-05 | 0.942 | 0.009987 | 0.0162 | nan | 1.084 |
| **14MeV** | 0.0004089 | 0.982 | 0.3169 | 0.681 | 0.5184 | 0.07844 | nan | 2.324 |
| **1MeV** | 0.0003907 | 0.916 | 0.002767 | 0.867 | 0.02587 | 0.01379 | nan | 1.185 |
| **1eV** | 4.497e-09 | 1.066 | 2.168e-09 | 1.053 | 0.01036 | 0.01685 | nan | 0.981 |
| **1keV** | 1.485e-06 | 0.968 | 1.187e-06 | 0.958 | 0.02296 | 0.01766 | nan | 1.065 |
| **6MeV** | 0.0005663 | 0.913 | 0.0535 | 0.710 | 0.1374 | 0.02949 | nan | 1.748 |

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

*Generat el 2026-06-17 per `scripts/compare_nto_to_truth.py`.*
