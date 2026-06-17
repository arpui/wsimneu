**Data:** 2026-06-17

# docs/site/nto/runs/nto_v110_Graphite/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_Graphite/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_Graphite/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/Graphite/truth/neutron_cascade_1.0MeV_20260617_024817.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 2.996e-06 | nan | 3.137e-10 | 1.367 | 0.0182 | 0.01836 | nan | 2.859 |
| **0.1eV** | 1.322e-07 | nan | 3.485e-10 | 0.894 | 0.01894 | 0.02227 | nan | 2.922 |
| **100eV** | 1.185e-06 | 9.241 | 1.02e-06 | 0.333 | 0.0562 | 0.04908 | nan | 3.103 |
| **100keV** | 0.00113 | 0.382 | 0.001122 | 0.321 | 0.06062 | 0.04999 | nan | 3.225 |
| **10eV** | 2.913e-06 | 16.195 | 1.012e-07 | 0.345 | 0.01855 | 0.03641 | nan | 3.064 |
| **10keV** | 0.0001163 | 1.055 | 0.0001068 | 0.326 | 0.03825 | 0.04517 | nan | 3.163 |
| **14MeV** | 0.01175 | 3.972 | 3.017 | 0.252 | 3.165 | 0.5679 | nan | 5.866 |
| **1MeV** | 0.00514 | 0.376 | 0.01388 | 0.274 | 0.2035 | 0.05879 | nan | 4.112 |
| **1eV** | 2.028e-08 | nan | 8.947e-09 | 0.426 | 0.04062 | 0.0343 | nan | 2.973 |
| **1keV** | 1.167e-05 | 10.862 | 1.058e-05 | 0.332 | 0.04074 | 0.04934 | nan | 3.106 |
| **6MeV** | 0.006387 | 0.767 | 0.2592 | 0.163 | 1.445 | 0.1499 | nan | 12.896 |

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
