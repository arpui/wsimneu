**Data:** 2026-06-18

# docs/site/nto/runs/nto_v110_DPE/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_DPE/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_DPE/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/DPE/truth/neutron_cascade_1.0MeV_20260617_114921.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 1.218e-08 | 1.339 | 1.149e-09 | 1.114 | 0.02894 | 0.01077 | nan | 0.935 |
| **0.1eV** | 4.524e-08 | 1.574 | 8.262e-10 | 1.088 | 0.02383 | 0.0188 | nan | 0.948 |
| **100eV** | 1.115e-07 | 2.299 | 1.028e-07 | 0.975 | 0.03512 | 0.02433 | nan | 1.049 |
| **100keV** | 0.000185 | 0.927 | 0.0001915 | 0.913 | 0.03826 | 0.01929 | nan | 1.118 |
| **10eV** | 2.407e-08 | 1.257 | 1.13e-08 | 1.021 | 0.02615 | 0.02134 | nan | 1.016 |
| **10keV** | 1.546e-05 | 0.948 | 1.528e-05 | 0.941 | 0.03879 | 0.02145 | nan | 1.086 |
| **14MeV** | 0.0003927 | 0.999 | 0.2965 | 0.693 | 0.5103 | 0.07717 | nan | 2.284 |
| **1MeV** | 0.0003934 | 0.934 | 0.003249 | 0.864 | 0.0261 | 0.02011 | nan | 1.189 |
| **1eV** | 1.492e-08 | 1.212 | 2.273e-09 | 1.177 | 0.01915 | 0.0244 | nan | 0.981 |
| **1keV** | 9.936e-07 | 4.787 | 7.72e-07 | 0.957 | 0.02436 | 0.0164 | nan | 1.065 |
| **6MeV** | 0.0004211 | 0.967 | 0.05403 | 0.709 | 0.1201 | 0.02845 | nan | 1.750 |

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
