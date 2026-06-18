**Data:** 2026-06-18

# docs/site/nto/runs/nto_v110_H2O/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_H2O/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_H2O/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/H2O/truth/neutron_cascade_1.0MeV_20260617_184633.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 9.889e-10 | 1.014 | 5.916e-10 | 0.962 | 0.004636 | 0.003735 | nan | 0.394 |
| **0.1eV** | 1.414e-09 | 1.025 | 1.667e-10 | 1.030 | 0.0038 | 0.006748 | nan | 0.399 |
| **100eV** | 1.451e-06 | 102.606 | 1.491e-06 | 2.595 | 0.02738 | 0.03238 | nan | 0.388 |
| **100keV** | 0.001147 | 2.871 | 0.001422 | 2.587 | 0.04866 | 0.0522 | nan | 0.392 |
| **10eV** | 1.525e-07 | 2.159 | 1.502e-07 | 2.425 | 0.02021 | 0.01965 | nan | 0.387 |
| **10keV** | 0.0001372 | 2.877 | 0.0001531 | 2.613 | 0.02965 | 0.04851 | nan | 0.388 |
| **14MeV** | 0.0012 | 2.124 | 0.1756 | 1.534 | 0.1865 | 0.04257 | nan | 1.066 |
| **1MeV** | 0.001434 | 2.701 | 0.01293 | 2.443 | 0.1163 | 0.05626 | nan | 0.417 |
| **1eV** | 5.966e-07 | 7.090 | 1.293e-08 | 1.626 | 0.00714 | 0.008417 | nan | 0.397 |
| **1keV** | 1.536e-05 | 2.628 | 1.41e-05 | 2.588 | 0.03746 | 0.04184 | nan | 0.392 |
| **6MeV** | 0.001423 | 2.581 | 0.05607 | 1.771 | 0.1837 | 0.05124 | nan | 0.724 |

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
