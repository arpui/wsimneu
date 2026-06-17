**Data:** 2026-06-17

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
| **0.025eV** | 3.628e-09 | 1.087 | 8.828e-10 | 1.094 | 0.04316 | 0.02969 | nan | 1.469 |
| **0.1eV** | 3.498e-09 | 1.071 | 3.857e-10 | 1.037 | 0.08564 | 0.02835 | nan | 1.502 |
| **100eV** | 2.957e-06 | 10.497 | 1.272e-06 | 0.607 | 0.1313 | 0.03714 | nan | 1.715 |
| **100keV** | 0.001683 | 0.673 | 0.001521 | 0.569 | 0.1327 | 0.0362 | nan | 1.824 |
| **10eV** | 4.857e-05 | 312.590 | 1.296e-07 | 0.640 | 0.1164 | 0.03783 | nan | 1.659 |
| **10keV** | 0.0001505 | 0.776 | 0.0001329 | 0.588 | 0.1525 | 0.04055 | nan | 1.770 |
| **14MeV** | 0.001419 | 0.815 | 0.9084 | 0.406 | 1.373 | 0.1716 | nan | 4.103 |
| **1MeV** | 0.002019 | 0.729 | 0.01429 | 0.573 | 0.1119 | 0.03266 | nan | 1.813 |
| **1eV** | 7.74e-09 | 0.902 | 8.803e-09 | 0.762 | 0.08673 | 0.03281 | nan | 1.586 |
| **1keV** | 1.483e-05 | 3.053 | 1.303e-05 | 0.595 | 0.1323 | 0.03941 | nan | 1.748 |
| **6MeV** | 0.001788 | 0.707 | 0.1469 | 0.478 | 0.437 | 0.05851 | nan | 2.874 |

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
