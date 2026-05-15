# 🏠 Neutron Cascade — Experiment Tracker

Tracking de runs d'entrenament del model neutron cascade amb condicionar per energia.

---

## Resum executiu

**Arquitectura actual**: EPiC-FM amb embedding linear continu (`condZ`), `sum_scale_nmax=True`.

**Referència seleccionada**: **run_010** (fs=2, condZ, 100k iter) — millor W1(z)=0.076 @1MeV, z_mean_bias≈0 a totes energies.

**Última actualització**: 2026-05-15

---

## Cronologia de runs

### Fase 1: Baseline — MS3 PASS (runs 001–002)

Entrenament inicial de l'arquitectura EPiC-FM sense conditioning per energia.

| Run | Config | Iteracions | Resultat |
|-----|--------|-----------|----------|
| [run_001](runs/run_001.md) | EPiC-FM, fs=1.0, 5E, sense condZ | 500k | ✅ **MS3 PASS** — baseline validada |
| [run_002](runs/run_002.md) | EPiC-FM, fs=5.0, 7E, sense condZ | 500k | ✅ Vàlid — fs sense condZ no ajuda (bias idèntic ≈ −1.5 cm) |

### Fase 2: Sweep fs×condZ — triar el millor feature_scale (runs 003–012)

Després del MS3, es va introduir `condZ` (normalització z energy-dependent) i es va fer un sweep de `feature_scale` (fs) per trobar el valor òptim.

**Energies de training**: `0.025eV, 1eV, 1keV, 100keV, 1MeV, 5MeV, 14.1MeV` (7E)

Els runs es van fer a 100k iteracions per explorar ràpid. Es van descartar runs que col·lapsaven (fs massa baix) o fallaven per errors de configuració.

#### Runs que van fallar

| Run | Config | Motiu |
|-----|--------|-------|
| run_003 | fs=5, FocalR, sum_scale=False | ❌ Col·lapse (experiment confundit) |
| run_004 | fs=5, condZ | ❓ Sense samples — probable col·lapse |
| run_005 | fs=1, condZ | ❌ Col·lapse confirmat (z_std≪truth) |

#### Runs que van funcionar (sweep fs)

| Run | fs | Iteracions | Col·lapse? | Qualitat | Veredicte |
|-----|----|-----------|-----------|----------|-----------|
| [run_006](runs/run_006.md) | 20 | 100k | ✅ NO | ✅ Alta, perfils nets | **Sweep guanyador** — Δ≈0 @1MeV |
| [run_007](runs/run_007.md) | 5 | 100k | ✅ NO | ⚠️ Baixa, tèrmics sorollosos | Insuficient |
| [run_008](runs/run_008.md) | 12 | 100k | ✅ NO | ⚠️ Similar a fs=5 | Insuficient |
| [run_009](runs/run_009.md) | 20 | 500k | ✅ NO | ✅ Alta | Full 500k — W1(log_edep) millora -50% |
| **[run_010](runs/run_010.md)** | **2** | **100k** | **✅ NO** | **✅ Alta** | **🏆 Referència seleccionada** |
| run_011 | 5 + edep_beta=2 | 100k | ✅ NO | ❌ Destruït a 5/14MeV | Rebutjat |
| run_012 | 3 + edep_beta=2 | 100k | ✅ NO | ❌ Mateix patró | Rebutjat |

#### Lliçons del sweep

1. **Llindar de col·lapse**: entre fs=1 (❌) i fs=5 (✅). Col·lapse brusc, no gradual.
2. **Gradient de qualitat**: fs=20 és millor que fs=5/12 en perfils tèrmics.
3. **fs=2 amb condZ funciona excepcionalment bé**: W1(z)=0.076 @1MeV — millor valor absolut de tot el sweep.
4. **edep_beta=2 rebutjat**: destrueix qualitat a energies altes sense cap benefici.
5. **500k no millora bias de z**: només millora W1(log_edep).

### **🏆 run_010 seleccionat com a referència**

Després del sweep fs×condZ, **run_010** (fs=2, condZ, 100k) va ser seleccionat com a referència per:

- **W1(z)=0.076 @1MeV** — el millor valor absolut de tots els runs
- **z_mean_bias≈0** a totes les energies ràpides (1, 5, 14.1 MeV)
- **Velocitat**: 100k iteracions (~15-30 min) vs 500k del guanyador fs=20
- **Edep_beta=0** (MSE pur), stable, sense hiperparàmetres addicionals

Aquest run es va utilitzar com a base per a l'avaluació d'interpolació i per a la fase 5b.1.

### Fase 3: Avaluació d'interpolació (eval_001)

Un cop seleccionat run_010 com a referència, es va avaluar la capacitat del model per **interpol·lar** entre energies no vistes durant l'entrenament.

**Energies de training** (les 7 del dataset): `0.025eV, 1eV, 1keV, 100keV, 1MeV, 5MeV, 14.1MeV`
**Energies de test** (NO presents al training): `10eV, 10keV, 2MeV, 10MeV`

Es va comparar run_010 (fs=2) vs run_007 (fs=5) per veure quin feature_scale generalitza millor.

| Mètrica | run_007 (fs=5) | run_010 (fs=2) | Guanyador |
|---------|---------------|---------------|:---------:|
| W1(z) avg (4 energies) | 1.026 | **0.785** | **run_010** |
| W1(log_edep) avg | 0.206 | **0.117** | **run_010** |
| edep_z_bias < 2cm | 4/4 | 4/4 | Empat |
| BAD W1(log_edep) | 3 | **2** | **run_010** |

**Resultat**: run_010 (fs=2) millora W1(z) 23-33% i W1(log_edep) 3-28% a totes les energies intermèdies.
El conditioning per energia (`condZ`) amb Linear embedding permet interpolació efectiva sense training addicional.

📊 [Veure avaluació completa d'interp_001](evals/interp_001.md)

### Fase 4: Phase 5b.1 — Model B (`n_energy_bins`) vs Linear control (runs 013–018)

Amb run_010 com a referència validada (inclòs l'interpolació), es va provar si un embedding discret per bins d'energia (`Model B`) podia millorar les ressonàncies.

| Run | Model | n_bins | Iteracions | Status |
|-----|-------|--------|------------|--------|
| [run_013](runs/run_013.md) | Linear (control) | — | 100k | ✅ Referència |
| [run_014](runs/run_014.md) | Model B | 8 | 100k | ❌ 6/7 energies BAD |
| [run_015](runs/run_015.md) | Model B | 16 | 100k | ❌ 3/7 energies BAD |
| [run_016](runs/run_016.md) | Model B | 32 | 100k | ❌ Marginal |
| [run_017](runs/run_017.md) | Model B | 64 | 100k | ✅ ≈ Linear |
| [run_018](runs/run_018.md) | Model B | 16 | 500k | ❌ Sense millora |

**Veredicte**: Model B amb `n_energy_bins` NO supera el Linear embedding. bins=64 (run_017) és equivalent a Linear en TOTES les mètriques, però no millora. L'interpolació validada a eval_001 confirma que Linear+condZ ja generalitza bé.

📋 [Veure comparativa completa](compare/compare_5b1.md)

---

## Decisions preses

| # | Decisió | Justificació | Runs |
|---|---------|-------------|------|
| 1 | **EPiC-FM + `sum_scale_nmax=True`** | Arquitectura base. `False` → mode collapse | Tots |
| 2 | **Linear embedding continu** (no Model B) | Model B no millora; Linear+condZ interpol·la bé | 013-018, interp_001 |
| 3 | **fs=2 amb condZ** — referència seleccionada | W1(z)=0.076 @1MeV, interpolació validada | run_010 |
| 4 | **Rebutjar edep_beta=2** | Destruïa qualitat sense benefici | 011, 012 |
| 5 | **Rebutjar Model B `n_energy_bins`** | bins=64 ≈ Linear, bins<64 degraduen | 014-018 |

[📚 Més detalls](basics/index.md)

## Com funciona aquesta pàgina

Cada run té la seva pàgina amb:
- Motivació i paràmetres de configuració
- Mètriques per energia (W1, bias, peak_r0)
- Gràfics A–F (transforms, z_phys, scatter, perfils)
- Links a runs comparats

Els gràfics es serveixen directament des del repo via GitHub.
