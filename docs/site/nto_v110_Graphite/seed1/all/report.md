**Data:** 2026-06-18

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
| **0.025eV** | 8.973e-12 | nan | 2.043e-10 | 1.001 | 0.07018 | 0.04364 | nan | 0.750 |
| **0.1eV** | 1.953e-11 | nan | 4.085e-10 | 1.239 | 0.0169 | 0.01274 | nan | 0.764 |
| **100eV** | 0.0004535 | 220.039 | 4.368e-07 | 1.276 | 0.01407 | 0.01058 | nan | 0.808 |
| **100keV** | 0.0007152 | 1.344 | 0.000378 | 1.243 | 0.01598 | 0.00677 | nan | 0.833 |
| **10eV** | 9.658e-07 | 1266.811 | 4.714e-08 | 1.284 | 0.02992 | 0.007406 | nan | 0.803 |
| **10keV** | 0.000282 | 2.602 | 4.057e-05 | 1.259 | 0.02466 | 0.00641 | nan | 0.819 |
| **14MeV** | 0.02561 | 8.925 | 1.78 | 0.582 | 2.051 | 0.4007 | nan | 2.540 |
| **1MeV** | 0.003572 | 1.481 | 0.001199 | 1.065 | 0.02301 | 0.006927 | nan | 1.056 |
| **1eV** | 6.266e-12 | nan | 4.831e-09 | 1.311 | 0.03746 | 0.00809 | nan | 0.780 |
| **1keV** | 1.326e-05 | 18.177 | 4.709e-06 | 1.284 | 0.01104 | 0.005979 | nan | 0.804 |
| **6MeV** | 0.01092 | 2.545 | 0.1351 | 0.542 | 0.8582 | 0.08454 | nan | 3.322 |

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
