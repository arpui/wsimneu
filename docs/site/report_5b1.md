# Report: Experiment 5b.1 — Resonance Conditioning (Model B)

**Data**: 2026-05-14  
**Estat**: ✅ Completat  
**Projecte**: Simneu — Surrogate generatiu de cascades de neutrons via EPiC-FM

---

## 1. Resum executiu

Es van executar 6 runs (013-018) per avaluar si un embedding discret d'energia (`n_energy_bins`) millora la reproducció de ressonàncies neutró-nucli respecte al Linear embedding sinusoidal continu.

**Veredicte**: Model B amb `n_energy_bins` **NO supera el Linear embedding.**
- **bins=64** (run_017) ≈ Linear en TOTES les mètriques
- **bins=8/16** degraduen a energies mitjanes (5MeV, 14.1MeV) amb W1_z > 2.0
- **bins=32** és intermedi, millor que 8/16 però pitjor que 64
- **200k iteracions** (run_018) no milloren el resultat → problema arquitectural

---

## 2. Configuração

Tots els runs comparteixen:

| Paràmetre | Valor |
|-----------|-------|
| Model | EPiC-FM (v5) |
| feature_scale | 2.0 |
| global_dim | 64 |
| batch_size | 256 |
| Learning rate | 3e-4 |
| focal_gamma | 0.0 (MSE pur) |
| sum_scale_nmax | True |
| energies | 7 (0.025eV – 14.1MeV) |

---

## 3. Taula de runs

| Run | Model | n_bins | Iteracions | W1(z) mitjana | W1(log_edep) mitjana | edep_z_bias mitjana | Veredicte |
|-----|-------|--------|------------|---------------|---------------------|-------------------|-----------|
| **013** | Linear | — | 100k | 0.24 | 0.066 | 0.32 | ✅ Control |
| **014** | Model B | 8 | 100k | 1.15 | 0.461 | 0.96 | ❌ Degradat |
| **015** | Model B | 16 | 100k | 0.94 | 0.379 | 1.48 | ❌ Degradat |
| **016** | Model B | 32 | 100k | 0.67 | 0.343 | 0.66 | ❌ Marginal |
| **017** | Model B | 64 | 100k | 0.24 | 0.067 | 0.34 | ✅ Acceptable |
| **018** | Model B | 16 | 200k | 0.91 | 0.376 | 1.47 | ❌ Degradat |

---

## 4. Mètriques detallades per energia

### 4.1 W1(z) — Distància en z (cm)

**Acceptable: < 1.0 cm** | Warning: < 2.0 cm | ❌: > 2.0 cm

| Energia      | run_013 | run_014 | run_015 | run_016 | run_017 | run_018 |
|--------------|---------|---------|---------|---------|---------|---------|
| 0.025eV      | 0.315 ✅ | 1.264 ⚠️ | 0.394 ✅ | 0.344 ✅ | 0.356 ✅ | 0.347 ✅ |
| 1eV          | 0.736 ✅ | 0.590 ✅ | 0.761 ✅ | 0.722 ✅ | 0.695 ✅ | 0.729 ✅ |
| 1keV         | 0.098 ✅ | 0.705 ✅ | 0.136 ✅ | 0.079 ✅ | 0.092 ✅ | 0.083 ✅ |
| 100keV       | 0.149 ✅ | 0.451 ✅ | 1.154 ⚠️ | 0.137 ✅ | 0.086 ✅ | 1.130 ⚠️ |
| 1MeV         | 0.146 ✅ | 1.384 ⚠️ | 0.958 ✅ | 0.153 ✅ | 0.140 ✅ | 0.929 ✅ |
| 5MeV         | 0.131 ✅ | 1.017 ⚠️ | 0.889 ✅ | 1.043 ⚠️ | 0.148 ✅ | 0.936 ✅ |
| 14.1MeV      | 0.131 ✅ | 2.664 ❌ | 2.279 ❌ | 2.222 ❌ | 0.187 ✅ | 2.206 ❌ |

### 4.2 W1(log_edep) — Distància en edep logarítmic

**Acceptable: < 0.10** | Warning: < 0.20 | ❌: > 0.20

| Energia      | run_013 | run_014 | run_015 | run_016 | run_017 | run_018 |
|--------------|---------|---------|---------|---------|---------|---------|
| 0.025eV      | 0.3222 ❌ | 0.3812 ❌ | 0.2998 ❌ | 0.3044 ❌ | 0.3099 ❌ | 0.2852 ❌ |
| 1eV          | 0.0618 ✅ | 0.0159 ✅ | 0.0581 ✅ | 0.0542 ✅ | 0.0690 ✅ | 0.0555 ✅ |
| 1keV         | 0.0120 ✅ | 0.2892 ❌ | 0.0109 ✅ | 0.0111 ✅ | 0.0168 ✅ | 0.0156 ✅ |
| 100keV       | 0.0108 ✅ | 0.2237 ❌ | 0.1666 ⚠️ | 0.0091 ✅ | 0.0110 ✅ | 0.1584 ⚠️ |
| 1MeV         | 0.0161 ✅ | 0.2995 ❌ | 0.1085 ⚠️ | 0.0206 ✅ | 0.0155 ✅ | 0.1130 ⚠️ |
| 5MeV         | 0.0217 ✅ | 0.6645 ❌ | 0.6486 ❌ | 0.6488 ❌ | 0.0281 ✅ | 0.6624 ❌ |
| 14.1MeV      | 0.0166 ✅ | 1.3551 ❌ | 1.3634 ❌ | 1.3543 ❌ | 0.0178 ✅ | 1.3450 ❌ |

### 4.3 edep_z_bias — Desplaçament centroid d'energia (cm)

**Acceptable: |·| < 2.0 cm** | Warning: |·| < 3.0 cm | ❌: > 3.0 cm

| Energia      | run_013 | run_014 | run_015 | run_016 | run_017 | run_018 |
|--------------|---------|---------|---------|---------|---------|---------|
| 0.025eV      | 0.49 ✅ | -0.59 ✅ | 0.50 ✅ | 0.46 ✅ | 0.55 ✅ | 0.50 ✅ |
| 1eV          | -0.96 ✅ | -0.43 ✅ | -0.96 ✅ | -0.88 ✅ | -0.92 ✅ | -0.94 ✅ |
| 1keV         | -0.28 ✅ | -1.66 ✅ | -0.28 ✅ | -0.32 ✅ | -0.41 ✅ | -0.19 ✅ |
| 100keV       | -0.24 ✅ | 0.84 ✅ | -4.25 ❌ | -0.19 ✅ | -0.15 ✅ | -4.23 ❌ |
| 1MeV         | -0.05 ✅ | 0.53 ✅ | 1.69 ✅ | -0.03 ✅ | -0.03 ✅ | 1.71 ✅ |
| 5MeV         | 0.06 ✅ | 2.05 ⚠️ | 1.60 ✅ | 1.60 ✅ | 0.02 ✅ | 1.68 ✅ |
| 14.1MeV      | -0.16 ✅ | -0.60 ✅ | -1.11 ✅ | -1.14 ✅ | -0.28 ✅ | -1.04 ✅ |

### 4.4 peak_r0_ratio — Proporció peak a r=0

**Acceptable: > 0.70** | Warning: > 0.65 | ❌: < 0.65

| Energia      | run_013 | run_014 | run_015 | run_016 | run_017 | run_018 |
|--------------|---------|---------|---------|---------|---------|---------|
| 0.025eV      | 1.872 ✅ | 1.834 ✅ | 2.120 ✅ | 2.068 ✅ | 2.028 ✅ | 2.030 ✅ |
| 1eV          | 1.337 ✅ | 1.528 ✅ | 1.328 ✅ | 1.360 ✅ | 1.327 ✅ | 1.346 ✅ |
| 1keV         | 0.990 ✅ | 0.970 ✅ | 1.000 ✅ | 0.983 ✅ | 1.008 ✅ | 1.007 ✅ |
| 100keV       | 1.005 ✅ | 0.977 ✅ | 0.987 ✅ | 0.982 ✅ | 1.030 ✅ | 0.993 ✅ |
| 1MeV         | 0.957 ✅ | 1.514 ✅ | 0.957 ✅ | 0.966 ✅ | 0.982 ✅ | 0.976 ✅ |
| 5MeV         | 0.950 ✅ | 0.996 ✅ | 1.151 ✅ | 1.154 ✅ | 0.966 ✅ | 1.209 ✅ |
| 14.1MeV      | 0.875 ✅ | 0.560 ❌ | 0.710 ✅ | 0.703 ✅ | 0.846 ✅ | 0.739 ✅ |

---

## 5. Anàlisi

### W1(z)
- **bins=64** és l'única versió Model B que manté W1(z) < 1.0 a totes les energies → equivalent a Linear
- **bins=8/16** fallen dramàticament a 5MeV i 14.1MeV (W1_z > 2.6)
- **bins=32** millora però continua fallant a 5MeV+
- **200k iteracions** (run_018) no milloren bins=16 → problema arquitectural

### W1(log_edep)
- **bins=64** ≈ Linear a energies baixes i mitjanes
- Tots els Model B tenen W1(log_edep) > 0.30 a 0.025eV (pitjor que Linear)
- Col·lapse total a 14.1MeV per bins=8/16/16-200k (W1 > 1.3)

### edep_z_bias
- Tots els runs passen el criteri a la majoria d'energies
- Run_015 (bins=16) té un outlier a 100keV: -4.25 cm

### peak_r0_ratio
- Tots els runs passen > 0.70 a la majoria d'energies
- A 14.1MeV, els Model B amb bins baixos fallen

---

## 6. Conclusions

Model B amb `n_energy_bins` **no és una millora** respecte a Linear embedding:
- El millor cas (bins=64) és equivalent, no superior
- Menys bins (8/16/32) degraduen significativament a energies altes
- La resolució de bins no captura millor les ressonàncies que l'embedding sinusoidal continu

---

## 7. Documents i figures

- [Pàgina principal del site](index.md)
- [Comparativa completa](compare/compare_5b1.md)
- [PDF unificat (6 runs)](images/results/figures/compare_5b1_unified_all6/compare_all.pdf)
- Pàgines de run individuals: [013](runs/run_013.md) [014](runs/run_014.md) [015](runs/run_015.md) [016](runs/run_016.md) [017](runs/run_017.md) [018](runs/run_018.md)

---

*Creat: 2026-05-15*
