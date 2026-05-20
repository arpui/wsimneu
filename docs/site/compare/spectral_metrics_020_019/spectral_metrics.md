# Spectral Metrics: run_019 (dim32) vs run_020 (dim64)

Three spectral metrics evaluated across all energies.

## Resonance Indicator (RI)
RI >0.80 OK | >0.60 WRN | <0.60 BAD

| Energy | run_019 (dim32) | run_020 (dim64) |
|--------|--------|--------|
| 0.025eV | 0.076 BAD | 0.077 BAD |
| 1eV | 0.096 BAD | 0.099 BAD |
| 1keV | 0.046 BAD | 0.046 BAD |
| 100keV | 0.046 BAD | 0.046 BAD |
| 1MeV | 0.089 BAD | 0.088 BAD |
| 5MeV | 0.081 BAD | 0.077 BAD |
| 14.1MeV | 0.121 BAD | 0.127 BAD |

## Peak Sharpness Ratio
>1.5 OK | >1.0 WRN | <1.0 BAD

| Energy | run_019 (dim32) | run_020 (dim64) |
|--------|--------|--------|
| 0.025eV | 4.846 OK | 5.072 OK |
| 1eV | 3.815 OK | 4.681 OK |
| 1keV | 14.776 OK | 9.780 OK |
| 100keV | 12.955 OK | 6.590 OK |
| 1MeV | 12.246 OK | 12.620 OK |
| 5MeV | 53.737 OK | 56.481 OK |
| 14.1MeV | 15.983 OK | 16.029 OK |

## Spectral W1 (log10-space, by energy bins)
W1 <0.20 OK | 0.20-0.50 WRN | >0.50 BAD

| Energy | W1(eV) | W1(keV) | W1(MeV) | run_019 (dim32) | run_020 (dim64) |
|--------|--------|--------|--------|--------|--------|
| 0.025eV | -- | 1.051 BAD | -- | -- | 1.051 BAD | -- |
| 1eV | -- | 1.050 BAD | -- | -- | 1.049 BAD | -- |
| 1keV | -- | 0.734 BAD | -- | -- | 0.734 BAD | -- |
| 100keV | -- | 0.834 BAD | -- | -- | 0.834 BAD | -- |
| 1MeV | -- | 0.904 BAD | -- | -- | 0.903 BAD | -- |
| 5MeV | -- | 0.934 BAD | 0.145 OK | -- | 0.933 BAD | 0.173 OK |
| 14.1MeV | -- | 0.826 BAD | 0.201 WRN | -- | 0.825 BAD | 0.192 OK |
