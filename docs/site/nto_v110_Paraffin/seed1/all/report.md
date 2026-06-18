**Data:** 2026-06-18

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
| **0.025eV** | 8.589e-10 | 129.226 | 4.478e-10 | 0.994 | 0.003443 | 0.005705 | nan | 0.379 |
| **0.1eV** | 1.441e-09 | 5.695 | 7.551e-10 | 1.055 | 0.002985 | 0.00793 | nan | 0.383 |
| **100eV** | 1.775e-06 | 2.829 | 1.485e-06 | 2.690 | 0.01969 | 0.03014 | nan | 0.373 |
| **100keV** | 0.001161 | 2.792 | 0.001438 | 2.705 | 0.04422 | 0.05651 | nan | 0.375 |
| **10eV** | 2.109e-07 | 20.331 | 1.526e-07 | 2.481 | 0.01421 | 0.02295 | nan | 0.377 |
| **10keV** | 0.0001493 | 2.810 | 0.0001402 | 2.727 | 0.03242 | 0.05326 | nan | 0.372 |
| **14MeV** | 0.001173 | 2.332 | 0.2415 | 1.972 | 0.2956 | 0.05277 | nan | 0.773 |
| **1MeV** | 0.001336 | 2.941 | 0.01379 | 2.683 | 0.1096 | 0.06945 | nan | 0.378 |
| **1eV** | 1.6e-08 | 17.885 | 1.441e-08 | 1.682 | 0.00722 | 0.02265 | nan | 0.376 |
| **1keV** | 1.531e-05 | 3.087 | 1.397e-05 | 2.697 | 0.02402 | 0.03758 | nan | 0.376 |
| **6MeV** | 0.001339 | 2.605 | 0.07555 | 2.430 | 0.2199 | 0.06155 | nan | 0.556 |

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
