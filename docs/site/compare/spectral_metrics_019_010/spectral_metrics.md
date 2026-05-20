# Spectral Metrics: run_010 vs run_019

Three spectral metrics evaluated across all energies.

## Resonance Indicator (RI)
RI >0.80 OK | >0.60 WRN | <0.60 BAD

| Energy | run_010 | run_019 |
|--------|--------|--------|
| 0.025eV | 0.077 BAD | 0.076 BAD |
| 1eV | 0.095 BAD | 0.096 BAD |
| 1keV | 0.045 BAD | 0.046 BAD |
| 100keV | 0.045 BAD | 0.046 BAD |
| 1MeV | 0.087 BAD | 0.089 BAD |
| 5MeV | 0.057 BAD | 0.081 BAD |
| 14.1MeV | 0.122 BAD | 0.121 BAD |

## Peak Sharpness Ratio
>1.5 OK | >1.0 WRN | <1.0 BAD

| Energy | run_010 | run_019 |
|--------|--------|--------|
| 0.025eV | 5.803 OK | 4.846 OK |
| 1eV | 4.757 OK | 3.815 OK |
| 1keV | 9.578 OK | 14.776 OK |
| 100keV | 8.606 OK | 12.955 OK |
| 1MeV | 11.436 OK | 12.246 OK |
| 5MeV | 55.427 OK | 53.737 OK |
| 14.1MeV | 16.227 OK | 15.983 OK |

## Spectral W1 (log10-space, by energy bins)
W1 <0.20 OK | 0.20-0.50 WRN | >0.50 BAD

| Energy | W1(eV) | W1(keV) | W1(MeV) | run_010 | run_019 |
|--------|--------|--------|--------|--------|--------|
| 0.025eV | -- | 1.051 BAD | -- | -- | 1.051 BAD | -- |
| 1eV | -- | 1.049 BAD | -- | -- | 1.050 BAD | -- |
| 1keV | -- | 0.734 BAD | -- | -- | 0.734 BAD | -- |
| 100keV | -- | 0.834 BAD | -- | -- | 0.834 BAD | -- |
| 1MeV | -- | 0.903 BAD | -- | -- | 0.904 BAD | -- |
| 5MeV | -- | 0.934 BAD | 0.158 OK | -- | 0.934 BAD | 0.145 OK |
| 14.1MeV | -- | 0.828 BAD | 0.207 WRN | -- | 0.826 BAD | 0.201 WRN |
