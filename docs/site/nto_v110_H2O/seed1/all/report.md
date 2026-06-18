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
| **0.025eV** | 7.551e-10 | 1.630 | 4.347e-10 | 0.969 | 0.01461 | 0.005359 | nan | 0.403 |
| **0.1eV** | 0.0002005 | 2245.578 | 4.797e-10 | 1.040 | 0.01351 | 0.005851 | nan | 0.407 |
| **100eV** | 0.0002189 | 173.231 | 1.453e-06 | 2.541 | 0.01745 | 0.02978 | nan | 0.396 |
| **100keV** | 0.001107 | 2.809 | 0.001338 | 2.533 | 0.04541 | 0.04969 | nan | 0.400 |
| **10eV** | 0.0006004 | 3315.798 | 1.385e-07 | 2.365 | 0.01323 | 0.01769 | nan | 0.398 |
| **10keV** | 0.0004039 | 8.051 | 0.0001442 | 2.538 | 0.02824 | 0.04478 | nan | 0.400 |
| **14MeV** | 0.0009866 | 2.008 | 0.1717 | 1.449 | 0.1677 | 0.04331 | nan | 1.128 |
| **1MeV** | 0.001402 | 2.835 | 0.01288 | 2.414 | 0.1093 | 0.06151 | nan | 0.424 |
| **1eV** | 1.571e-06 | 2232.308 | 1.306e-08 | 1.616 | 0.004695 | 0.01011 | nan | 0.405 |
| **1keV** | 0.0006133 | 23.796 | 1.342e-05 | 2.518 | 0.01846 | 0.03687 | nan | 0.403 |
| **6MeV** | 0.001116 | 2.400 | 0.05691 | 1.749 | 0.1616 | 0.04746 | nan | 0.734 |

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
