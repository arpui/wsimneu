**Data:** 2026-06-17

# docs/site/nto/runs/nto_v110_AirHumid/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_AirHumid/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_AirHumid/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/AirHumid/truth/neutron_cascade_1.0MeV_20260617_024814.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 2.683 | 4552693248.000 | 1.81e-08 | 205.167 | 13.63 | 0.6464 | nan | 6.498 |
| **0.1eV** | 2.084 | 3149323008.000 | 2.364e-07 | 790.230 | 12.02 | 0.6697 | nan | 9.051 |
| **100eV** | 1.989 | 6060163.000 | 5.371e-06 | 19.275 | 10.81 | 0.7033 | nan | 22.924 |
| **100keV** | 2.164 | 20773.223 | 0.005107 | 43.554 | 10.81 | 0.7043 | nan | 32.842 |
| **10eV** | 2.005 | 56613516.000 | 1.014e-06 | 32.992 | 11.11 | 0.6967 | nan | 18.126 |
| **10keV** | 2.106 | 73499.469 | 0.0009071 | 27.662 | 10.71 | 0.7048 | nan | 29.877 |
| **14MeV** | 2.287 | 888955.688 | 6.331 | 575.750 | 14.41 | 0.5673 | nan | 3.537 |
| **1MeV** | 2.554 | 137499.719 | 8.304 | 20553.463 | 11.87 | 0.7107 | nan | 18.292 |
| **1eV** | 2.03 | 541263040.000 | 2.449e-07 | 77.461 | 11.45 | 0.6863 | nan | 13.313 |
| **1keV** | 2.012 | 792307.688 | 5.419e-05 | 27.374 | 10.74 | 0.7065 | nan | 27.056 |
| **6MeV** | 2.87 | 904401.250 | 2.771 | 764.923 | 13.07 | 0.686 | nan | 9.825 |

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
