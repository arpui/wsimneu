**Data:** 2026-06-17

# docs/site/nto/runs/nto_v110_Paraffin/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_Paraffin/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_Paraffin/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/Paraffin/truth/neutron_cascade_1.0MeV_20260617_024816.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 2.536e-09 | 17.811 | 6.46e-10 | 1.037 | 0.006365 | 0.008103 | 9363 | 0.958 |
| **0.1eV** | 2.336e-09 | 81.812 | 4.62e-10 | 1.036 | 0.009382 | 0.007222 | 9281 | 0.979 |
| **100eV** | 5.978e-08 | 40.960 | 9.165e-08 | 1.029 | 0.01031 | 0.007111 | nan | 0.986 |
| **100keV** | 3.365e-05 | 1.057 | 4.54e-05 | 1.019 | 0.009893 | 0.005385 | 8203 | 0.994 |
| **10eV** | 3.279e-08 | 27.557 | 8.658e-09 | 1.048 | 0.008297 | 0.01162 | 8912 | 0.966 |
| **10keV** | 2.396e-05 | 1.102 | 3.245e-06 | 1.037 | 0.009782 | 0.01159 | nan | 0.979 |
| **14MeV** | 0.0002846 | 0.810 | 0.07239 | 0.668 | 0.2116 | 0.01432 | 7858 | 2.281 |
| **1MeV** | 0.0001227 | 1.083 | 0.0008643 | 1.010 | 0.01094 | 0.008109 | 8008 | 1.005 |
| **1eV** | 2.342e-09 | 1142.726 | 1.215e-09 | 1.039 | 0.008531 | 0.007304 | 9136 | 0.971 |
| **1keV** | 0.0002011 | 5.336 | 4.684e-07 | 1.065 | 0.01067 | 0.007263 | nan | 0.952 |
| **6MeV** | 0.000239 | 0.950 | 0.01204 | 0.790 | 0.05429 | 0.007273 | 7910 | 1.480 |

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
