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
| **0.025eV** | 6.416e-06 | 291.350 | 8.749e-10 | 0.896 | 0.02605 | 0.006493 | 2.372e+04 | 1.042 |
| **0.1eV** | 6.23e-09 | 1.116 | 8.516e-10 | 0.960 | 0.02244 | 0.007489 | 1.618e+04 | 1.070 |
| **100eV** | 2.125e-07 | 1.027 | 1.494e-07 | 0.916 | 0.02685 | 0.007618 | 1611 | 1.131 |
| **100keV** | 0.0009886 | 0.398 | 0.001258 | 0.310 | 0.6943 | 0.04185 | 386.6 | 5.326 |
| **10eV** | 8.928e-09 | 1.016 | 1.378e-08 | 0.936 | 0.02452 | 0.007789 | 3172 | 1.093 |
| **10keV** | 2.295e-05 | 0.863 | 1.895e-05 | 0.811 | 0.004726 | 0.008475 | 556.6 | 1.279 |
| **14MeV** | 0.00513 | 0.735 | 2.008 | 1.316 | 0.1293 | 0.009429 | 0.5546 | 0.905 |
| **1MeV** | 0.01434 | 0.287 | 0.04581 | 0.142 | 1.967 | 0.2246 | 304.2 | 13.881 |
| **1eV** | 2.649e-09 | 1.013 | 4.293e-09 | 0.934 | 0.03555 | 0.008021 | 7111 | 1.091 |
| **1keV** | 2.208e-06 | 0.952 | 1.696e-06 | 0.883 | 0.01533 | 0.006916 | 837.6 | 1.177 |
| **6MeV** | 0.009184 | 1.713 | 0.2339 | 1.019 | 0.122 | 0.01762 | 0.6045 | 1.049 |

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
