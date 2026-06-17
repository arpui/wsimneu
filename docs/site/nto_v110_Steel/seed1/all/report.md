**Data:** 2026-06-17

# docs/site/nto/runs/nto_v110_Steel/seed1/all — NTO vs Truth Geant4

Report comparatiu d'una iteració del **Neural Transition Operator** sobre cascades del primary neutron.

## Configuració

- **Compare data**: `runs/nto_v110_Steel/v110_all/compare_data.h5`
- **Generated**: `runs/nto_v110_Steel/v110_all/samples_all_E.h5`
- **Truth patterns**: `data/geant4/simg4/Steel/truth/neutron_cascade_2.500e-02eV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_1.000e-01eV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_1.000eV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_10.000eV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_0.10keV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_1.00keV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_10.00keV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_0.1MeV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_1.0MeV_20260616_222818.h5,data/geant4/simg4/Steel/truth/neutron_cascade_6.0MeV_*.h5,data/geant4/simg4/Steel/truth/neutron_cascade_14.1MeV_*.h5`
- **Tolerància energia**: ±20%

## Mètriques per energia

Llegenda: `W1` = Wasserstein-1 (més baix → millor); `mean_ratio` = `gen.mean / truth.mean` (~1.0 = mitjana ben capturada).

| Energia | edep W1 | edep ratio | ΔE_kin W1 | ΔE_kin ratio | Δd W1 | cos(θ) W1 | Δt W1 | N_hits ratio |
|---|---|---|---|---|---|---|---|---|
| **0.025eV** | 0.009113 | 317275.000 | 3.49e-09 | 0.583 | 0.438 | 0.05288 | 1.707e+04 | 1.640 |
| **0.1eV** | 0.0003582 | 15483.518 | 8.394e-10 | 0.974 | 0.0216 | 0.01492 | 1.622e+04 | 1.065 |
| **100eV** | 9.573e-08 | 29.847 | 1.302e-07 | 0.915 | 0.01222 | 0.007426 | 1527 | 1.132 |
| **100keV** | 0.0009816 | 0.347 | 0.001244 | 0.310 | 0.6948 | 0.03807 | 350.5 | 5.325 |
| **10eV** | 7.167e-09 | 10.857 | 2.073e-08 | 0.927 | 0.006975 | 0.006853 | 3144 | 1.104 |
| **10keV** | 1.897e-05 | 0.782 | 1.938e-05 | 0.805 | 0.01321 | 0.005856 | 492.3 | 1.289 |
| **14MeV** | 0.004917 | 1.595 | 1.852 | 1.291 | 0.3276 | 0.0115 | 0.0333 | 0.922 |
| **1MeV** | 0.01366 | 0.260 | 0.04605 | 0.157 | 1.857 | 0.2243 | 241.5 | 12.583 |
| **1eV** | 4.54e-09 | 1.065 | 2.331e-09 | 0.971 | 0.006629 | 0.008879 | 7067 | 1.054 |
| **1keV** | 1.398e-06 | 0.858 | 1.645e-06 | 0.867 | 0.009034 | 0.006632 | 808.9 | 1.199 |
| **6MeV** | 0.00269 | 1.262 | 0.2238 | 1.004 | 0.0868 | 0.03007 | 0.02505 | 1.064 |

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
