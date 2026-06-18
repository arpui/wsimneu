**Data:** 2026-06-18

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
| **0.025eV** | 2.469 | 4190092032.000 | 3.636e-09 | 41.431 | 13.31 | 0.6916 | nan | 7.813 |
| **0.1eV** | 2.178 | 3292083200.000 | 1.358e-08 | 45.832 | 12.33 | 0.7099 | nan | 10.358 |
| **100eV** | 1.92 | 5916892.500 | 3.706e-06 | 12.259 | 10.65 | 0.7177 | nan | 24.621 |
| **100keV** | 1.925 | 18652.572 | 0.002819 | 17.897 | 10.37 | 0.7235 | nan | 33.706 |
| **10eV** | 1.945 | 55869076.000 | 4.679e-07 | 14.567 | 10.99 | 0.7176 | nan | 19.886 |
| **10keV** | 1.864 | 66248.547 | 0.0002756 | 10.611 | 10.33 | 0.7195 | nan | 32.626 |
| **14MeV** | 1.911 | 742639.250 | 3.438 | 310.405 | 14.19 | 0.6267 | nan | 4.763 |
| **1MeV** | 2.124 | 115350.141 | 0.03461 | 54.387 | 10.99 | 0.7317 | nan | 28.449 |
| **1eV** | 1.992 | 537750848.000 | 6.675e-08 | 20.403 | 11.41 | 0.7095 | nan | 15.018 |
| **1keV** | 1.883 | 734739.062 | 3.155e-05 | 13.000 | 10.5 | 0.7212 | nan | 29.269 |
| **6MeV** | 2.385 | 751728.688 | 0.618 | 168.195 | 12.58 | 0.7023 | nan | 9.799 |

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
