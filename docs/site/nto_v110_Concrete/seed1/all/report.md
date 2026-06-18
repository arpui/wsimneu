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
| **0.025eV** | 6.023e-06 | 84.940 | 7.098e-10 | 1.071 | 0.0307 | 0.008849 | nan | 0.907 |
| **0.1eV** | 7.336e-06 | 2796.629 | 8.232e-10 | 1.080 | 0.03318 | 0.0129 | nan | 0.931 |
| **100eV** | 2.771e-07 | 1.608 | 1.135e-07 | 0.989 | 0.02346 | 0.01126 | nan | 1.030 |
| **100keV** | 0.000244 | 0.937 | 0.0002421 | 0.887 | 0.03295 | 0.01654 | nan | 1.157 |
| **10eV** | 1.079e-06 | 45.232 | 7.518e-09 | 1.021 | 0.01713 | 0.01634 | nan | 0.999 |
| **10keV** | 1.715e-05 | 0.967 | 1.409e-05 | 0.934 | 0.01071 | 0.01756 | nan | 1.089 |
| **14MeV** | 0.001503 | 1.380 | 1.333 | 0.645 | 1.919 | 0.1862 | nan | 2.323 |
| **1MeV** | 0.001205 | 0.892 | 0.00637 | 0.780 | 0.1032 | 0.01493 | nan | 1.429 |
| **1eV** | 8.705e-08 | 5.293 | 1.267e-09 | 1.062 | 0.02854 | 0.009237 | nan | 0.963 |
| **1keV** | 2.318e-06 | 1.242 | 1.328e-06 | 0.974 | 0.03146 | 0.0131 | nan | 1.067 |
| **6MeV** | 0.001232 | 0.951 | 0.2374 | 0.519 | 1.197 | 0.0831 | nan | 3.104 |

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
