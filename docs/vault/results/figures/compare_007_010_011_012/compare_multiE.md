# Comparació de runs nc_multiE

Runs: run_007 (fs=5, baseline), run_010 (fs=2), run_011 (fs=5+edep), run_012 (fs=3+edep)


## edep_z_bias [cm]

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK +0.29 | OK +0.70 | OK +0.15 | OK +0.49 |
| 1eV | OK -1.04 | OK -0.78 | OK -0.94 | OK -0.78 |
| 1keV | OK -0.42 | OK -0.24 | OK +0.14 | OK +0.32 |
| 100keV | OK -0.35 | OK -0.23 | OK +0.20 | OK +0.27 |
| 1MeV | OK -0.06 | OK -0.06 | OK +0.22 | OK +0.35 |
| 5MeV | OK -0.12 | OK +0.07 | OK +1.15 | OK +1.67 |
| 14.1MeV | OK -0.39 | OK -0.04 | OK -1.67 | OK -1.44 |

## z_mean_bias [cm]

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK -0.44 | OK -0.15 | OK +0.36 | OK +0.49 |
| 1eV | OK -0.83 | OK -0.63 | OK +0.05 | OK +0.06 |
| 1keV | OK -0.17 | OK -0.11 | WRN +1.51 | WRN +1.45 |
| 100keV | OK -0.17 | OK -0.13 | OK +0.77 | OK +0.75 |
| 1MeV | OK -0.07 | OK -0.01 | OK +0.09 | OK +0.18 |
| 5MeV | OK -0.19 | OK +0.10 | WRN +1.74 | WRN +2.15 |
| 14.1MeV | OK -0.38 | OK +0.02 | WRN -2.12 | WRN -1.87 |

## z_std σ_gen/σ_truth

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK 0.953 | OK 1.014 | OK 1.015 | OK 1.035 |
| 1eV | OK 0.880 | OK 0.918 | OK 0.955 | OK 0.971 |
| 1keV | OK 0.954 | OK 0.974 | OK 1.127 | OK 1.145 |
| 100keV | OK 0.950 | OK 0.960 | OK 1.120 | OK 1.124 |
| 1MeV | OK 0.952 | OK 0.965 | OK 1.048 | OK 1.054 |
| 5MeV | OK 0.985 | OK 0.997 | WRN 1.232 | WRN 1.269 |
| 14.1MeV | OK 1.003 | OK 1.003 | OK 0.966 | OK 0.976 |

## r_std σ_gen/σ_truth

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK 0.979 | OK 0.988 | OK 1.027 | OK 1.029 |
| 1eV | OK 0.971 | OK 0.972 | OK 1.036 | OK 1.020 |
| 1keV | OK 0.992 | OK 0.989 | OK 1.139 | OK 1.127 |
| 100keV | OK 0.991 | OK 0.989 | OK 1.142 | OK 1.141 |
| 1MeV | OK 0.985 | OK 0.981 | OK 1.086 | OK 1.086 |
| 5MeV | OK 0.976 | OK 0.979 | OK 1.075 | OK 1.093 |
| 14.1MeV | OK 0.965 | OK 0.962 | OK 0.996 | OK 0.993 |

## edep_std σ_gen/σ_truth (log)

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK 1.003 | OK 1.003 | OK 0.825 | OK 0.896 |
| 1eV | OK 0.994 | OK 0.993 | WRN 0.686 | WRN 0.792 |
| 1keV | OK 0.990 | OK 0.990 | WRN 0.625 | WRN 0.737 |
| 100keV | OK 0.992 | OK 0.993 | BAD 0.357 | BAD 0.472 |
| 1MeV | OK 0.990 | OK 0.988 | BAD 0.297 | BAD 0.397 |
| 5MeV | OK 0.999 | OK 1.000 | BAD 0.274 | BAD 0.375 |
| 14.1MeV | OK 0.992 | OK 0.996 | BAD 0.316 | BAD 0.368 |

## nhits_ratio

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | WRN 1.114 | WRN 1.114 | WRN 1.114 | WRN 1.114 |
| 1eV | OK 1.030 | OK 1.030 | OK 1.030 | OK 1.030 |
| 1keV | OK 1.008 | OK 1.008 | OK 1.008 | OK 1.008 |
| 100keV | OK 0.999 | OK 0.999 | OK 0.999 | OK 0.999 |
| 1MeV | OK 0.998 | OK 0.998 | OK 0.998 | OK 0.998 |
| 5MeV | OK 0.996 | OK 0.996 | OK 0.996 | OK 0.996 |
| 14.1MeV | OK 1.007 | OK 1.007 | OK 1.007 | OK 1.007 |

## nhits_std σ_gen/σ_truth

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK 0.998 | OK 0.998 | OK 0.998 | OK 0.998 |
| 1eV | OK 1.000 | OK 1.000 | OK 1.000 | OK 1.000 |
| 1keV | OK 1.002 | OK 1.002 | OK 1.002 | OK 1.002 |
| 100keV | OK 1.001 | OK 1.001 | OK 1.001 | OK 1.001 |
| 1MeV | OK 1.003 | OK 1.003 | OK 1.003 | OK 1.003 |
| 5MeV | OK 0.969 | OK 0.969 | OK 0.969 | OK 0.969 |
| 14.1MeV | OK 0.999 | OK 0.999 | OK 0.999 | OK 0.999 |

## peak_r0_ratio

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | WRN 1.808 | WRN 1.914 | WRN 2.261 | WRN 2.000 |
| 1eV | WRN 1.269 | WRN 1.464 | WRN 1.575 | OK 1.109 |
| 1keV | OK 0.957 | OK 0.983 | OK 0.929 | OK 0.877 |
| 100keV | OK 0.990 | OK 1.001 | OK 1.215 | OK 1.168 |
| 1MeV | OK 0.928 | OK 0.954 | OK 1.124 | OK 1.089 |
| 5MeV | OK 0.924 | OK 0.943 | OK 1.161 | OK 1.118 |
| 14.1MeV | OK 0.865 | OK 0.872 | OK 0.942 | OK 0.915 |

## Pearson(z,logE) gen

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | 0.396 | 0.392 | 0.334 | 0.356 |
| 1eV | 0.318 | 0.321 | 0.235 | 0.262 |
| 1keV | 0.292 | 0.290 | 0.330 | 0.332 |
| 100keV | 0.153 | 0.157 | 0.211 | 0.192 |
| 1MeV | 0.095 | 0.094 | 0.002 | 0.037 |
| 5MeV | 0.026 | 0.017 | -0.124 | -0.077 |
| 14.1MeV | -0.034 | -0.036 | -0.115 | -0.098 |

## Pearson(z,logE) truth

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | 0.346 | 0.346 | 0.346 | 0.346 |
| 1eV | 0.315 | 0.315 | 0.315 | 0.315 |
| 1keV | 0.297 | 0.297 | 0.297 | 0.297 |
| 100keV | 0.163 | 0.163 | 0.163 | 0.163 |
| 1MeV | 0.093 | 0.093 | 0.093 | 0.093 |
| 5MeV | 0.012 | 0.012 | 0.012 | 0.012 |
| 14.1MeV | -0.040 | -0.040 | -0.040 | -0.040 |

## W1(z) [cm]

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | OK 0.653 | OK 0.464 | OK 0.389 | OK 0.457 |
| 1eV | OK 0.827 | OK 0.625 | OK 0.507 | OK 0.402 |
| 1keV | OK 0.229 | OK 0.180 | WRN 1.500 | WRN 1.428 |
| 100keV | OK 0.300 | OK 0.244 | OK 0.990 | OK 0.942 |
| 1MeV | OK 0.109 | OK 0.076 | OK 0.740 | OK 0.710 |
| 5MeV | OK 0.241 | OK 0.245 | BAD 2.005 | BAD 2.448 |
| 14.1MeV | OK 0.474 | OK 0.165 | BAD 2.160 | WRN 1.909 |

## W1(log_edep)

| Energia | run_007 (fs=5, baseline) | run_010 (fs=2) | run_011 (fs=5+edep) | run_012 (fs=3+edep) |
|---------|-------:|-------:|-------:|-------:|
| 0.025eV | BAD 0.341 | BAD 0.323 | BAD 1.259 | BAD 1.001 |
| 1eV | OK 0.067 | OK 0.058 | BAD 1.819 | BAD 1.550 |
| 1keV | OK 0.031 | OK 0.025 | BAD 1.839 | BAD 1.593 |
| 100keV | OK 0.027 | OK 0.030 | BAD 1.782 | BAD 1.641 |
| 1MeV | OK 0.030 | OK 0.034 | BAD 1.901 | BAD 1.784 |
| 5MeV | OK 0.021 | OK 0.024 | BAD 4.285 | BAD 4.167 |
| 14.1MeV | OK 0.021 | OK 0.020 | BAD 1.466 | BAD 1.422 |