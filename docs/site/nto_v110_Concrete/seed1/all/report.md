**Data:** 2026-06-18

# docs/site/nto/runs/nto_v110_Concrete/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_Concrete/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_Concrete/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/Concrete/truth/neutron_cascade_1.0MeV_20260617_113113.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 5.262e-08 | 2.964 | 9.651e-10 | 1.064 | 0.04886 | 0.009005 | nan | 0.994 |
| **0.1eV** | 9.795e-09 | 192.800 | 9.644e-10 | 1.071 | 0.02978 | 0.006576 | nan | 1.007 |
| **100eV** | 1.934e-07 | 1.761 | 9.881e-08 | 0.950 | 0.02114 | 0.01158 | nan | 1.072 |
| **100keV** | 0.0003466 | 0.870 | 0.0003335 | 0.880 | 0.04348 | 0.01296 | nan | 1.167 |
| **10eV** | 3.282e-06 | 88.658 | 1.104e-08 | 0.977 | 0.02231 | 0.009377 | nan | 1.047 |
| **10keV** | 0.0001689 | 1.229 | 2.039e-05 | 0.912 | 0.0415 | 0.0102 | nan | 1.116 |
| **14MeV** | 0.0006473 | 1.085 | 2.111 | 0.387 | 2.279 | 0.2357 | nan | 3.878 |
| **1MeV** | 0.001311 | 0.838 | 0.005354 | 0.775 | 0.1035 | 0.01119 | nan | 1.438 |
| **1eV** | 7.246e-09 | 13.679 | 1.208e-09 | 1.022 | 0.01857 | 0.007019 | nan | 1.023 |
| **1keV** | 2.513e-06 | 2.884 | 1.835e-06 | 0.924 | 0.008276 | 0.01159 | nan | 1.100 |
| **6MeV** | 0.001082 | 0.923 | 0.2776 | 0.424 | 1.196 | 0.08189 | nan | 3.793 |

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
