# Comparació de runs nc_multiE

Runs: run_019

> **NOTE:** Mètriques calculades amb merged HDF5 corregit (z_mean=-18.05 del training set). Original merged tenia z_mean=-22.57 (copiat de fitxer 0.1eV).

## edep_z_bias [cm]

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK -1.38 |
| 0p5eV | OK -1.08 |
| 5eV | OK -0.60 |
| 20eV | OK -0.59 |
| 50keV | - |
| 500keV | OK -0.37 |
| 3MeV | BAD -4.44 |
| 8MeV | BAD -6.38 |

## z_peak_bias [cm]

| Energia | run_019 |
|---------|-------:|
| 0p1eV | +1.00 |
| 0p5eV | -1.02 |
| 5eV | +1.00 |
| 20eV | +1.96 |
| 50keV | - |
| 500keV | +0.43 |
| 3MeV | -4.51 |
| 8MeV | -5.54 |

## z_std σ_gen/σ_truth

| Energia | run_019 |
|---------|-------:|
| 0p1eV | WRN 0.781 |
| 0p5eV | OK 0.879 |
| 5eV | OK 0.856 |
| 20eV | OK 0.833 |
| 50keV | WRN 0.766 |
| 500keV | WRN 0.766 |
| 3MeV | WRN 0.724 |
| 8MeV | OK 0.902 |

## r_std σ_gen/σ_truth

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK 0.994 |
| 0p5eV | OK 1.003 |
| 5eV | OK 0.956 |
| 20eV | OK 0.952 |
| 50keV | OK 0.996 |
| 500keV | OK 0.986 |
| 3MeV | OK 0.919 |
| 8MeV | OK 0.954 |

## edep_std σ_gen/σ_truth (log)

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK 1.056 |
| 0p5eV | OK 1.043 |
| 5eV | OK 1.029 |
| 20eV | OK 0.990 |
| 50keV | OK 0.826 |
| 500keV | OK 0.864 |
| 3MeV | OK 0.973 |
| 8MeV | OK 1.013 |

## nhits_ratio

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK 0.993 |
| 0p5eV | OK 1.029 |
| 5eV | OK 0.978 |
| 20eV | OK 1.003 |
| 50keV | OK 0.979 |
| 500keV | OK 0.989 |
| 3MeV | OK 1.000 |
| 8MeV | OK 1.010 |

## nhits_std σ_gen/σ_truth

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK 0.938 |
| 0p5eV | OK 0.994 |
| 5eV | OK 1.003 |
| 20eV | OK 1.014 |
| 50keV | OK 0.989 |
| 500keV | OK 1.005 |
| 3MeV | OK 1.001 |
| 8MeV | OK 0.984 |

## peak_r0_ratio

| Energia | run_019 |
|---------|-------:|
| 0p1eV | WRN 1.503 |
| 0p5eV | OK 1.169 |
| 5eV | WRN 0.431 |
| 20eV | WRN 0.557 |
| 50keV | OK 1.064 |
| 500keV | OK 0.939 |
| 3MeV | OK 0.972 |
| 8MeV | OK 0.948 |

## Pearson(z,logE) gen

| Energia | run_019 |
|---------|-------:|
| 0p1eV | 0.350 |
| 0p5eV | 0.333 |
| 5eV | 0.353 |
| 20eV | 0.339 |
| 50keV | 0.186 |
| 500keV | 0.116 |
| 3MeV | 0.050 |
| 8MeV | -0.016 |

## Pearson(z,logE) truth

| Energia | run_019 |
|---------|-------:|
| 0p1eV | 0.336 |
| 0p5eV | 0.332 |
| 5eV | 0.344 |
| 20eV | 0.352 |
| 50keV | 0.189 |
| 500keV | 0.116 |
| 3MeV | 0.045 |
| 8MeV | -0.022 |

## W1(z) [cm]

| Energia | run_019 |
|---------|-------:|
| 0p1eV | WRN 1.632 |
| 0p5eV | WRN 1.221 |
| 5eV | OK 0.598 |
| 20eV | WRN 1.036 |
| 50keV | - |
| 500keV | OK 0.405 |
| 3MeV | BAD 5.799 |
| 8MeV | BAD 7.769 |

## W1(log_edep)

| Energia | run_019 |
|---------|-------:|
| 0p1eV | OK 0.040 |
| 0p5eV | OK 0.040 |
| 5eV | OK 0.036 |
| 20eV | OK 0.035 |
| 50keV | - |
| 500keV | OK 0.048 |
| 3MeV | OK 0.080 |
| 8MeV | WRN 0.148 |
