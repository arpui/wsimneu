# 🏠 Neutron Cascade — Experiment Tracker

Tracking de runs d'entrenament del model neutron cascade amb condicionar per energia.

---

## Resum executiu

**Arquitectura actual** (tancada 🔒): EPiC-FM + Fourier dim=32 + embedding linear continu (`condZ`), `sum_scale_nmax=True`.
Flux complet:

EpicFMModel.forward(p_t, g_init, t, E_in, mask) → velocitat per hit (B, N, 4). 
Ni veu ni prediu N.
Les EPiC layers fan: pool global g ← φ_g(g, sum_pool(P), mean_pool(P), cond) → update per hit p'_i ← φ_p(p_i, g', cond). El vector global g és l'únic mecanisme de coordinació inter-hit.
El Fourier embedding (FourierFeatureEmbedding) s'aplica sobre edep abans de hit_in, per trencar el spectral bias i aprendre pics fins. És un buffer fix (register_buffer), no s'entrena.
Condicionament per E: o bé Linear(1→64) (continu) o bé Embedding(n_bins, 64) (discret, "Model B").

**Referència seleccionada**: ⭐ **run_019** (Fourier dim=32, fs=2, condZ, 100k iter) — captura millor pics de ressonància (Peak Sharpness +45% a 1keV), mètriques ≈ run_010, visual superior en three_curves_edep.png.

**Fase actual**: **Fase 7A finalitzada** (2026-05-18). run_019 consolidat com a referència definitiva.

**Millores futures** (planificades):
- Loss espectral (ADR-011): 4 opcions dissenyades, pendents de validació
- Track 2 — Discrete FM + procID (ADR-009): recerca oberta, branca paral·lela

**Última actualització**: 2026-05-19

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

### Fase 5: Fourier features per edep (runs 019-020)

Amb Model B descartat, es va provar una altra estratègia per millorar la captura de ressonàncies: **Fourier feature embedding** per al canal `edep`. En lloc de passar `edep` directament a la xarxa, es transforma amb sinus/cosinus d'altes freqüències — una tècnica que facilita que les MLP aprenguin funcions d'alta freqüència.

**Hipòtesi**: Més freqüències Fourier = millor captura de pics de ressonància (keV–MeV) = Peak Sharpness més alt.

**Experiment**: Es va comparar Fourier dim=32 vs dim=64, amb Linear (run_010) com a control.

| Run | Configuració | Iteracions | Status |
|-----|-------------|-----------|--------|
| [run_019](runs/run_019.md) | Fourier dim=32 + Linear | 100k | ✅ Referència actual |
| [run_020](runs/run_020.md) | Fourier dim=64 + Linear | 100k | ❌ Hipòtesi refutada |
| [run_021](runs/run_021.md) | GaussianRFF E_in dim=32 + Fourier edep | 100k | ⏳ Post-run pending |

**Resultats run_019 (Fourier dim=32)**:
- **Mètriques quantitatives**: essencialment idèntiques a Linear (run_010) — W1(z), W1(log_edep), bias són comparables
- **Visual (three_curves_edep.png)**: run_019 **captura millor els pics de ressonància** a energies eV–keV — els pics fins del truth són més visibles amb Fourier
- **Peak Sharpness**: lleugerament millor a 1keV (14.78 vs 9.58) i 100keV (12.96 vs 8.61), confirma pics més definits
- **W1(log_edep)** a 1eV: lleugerament pitjor (WRN 0.106 vs OK 0.058), però millora a 1keV–14MeV
- **Conclusió**: Fourier dim=32 millora la forma espectral fina visualment sense degradar mètriques. Esdevé la **referència actual**.

**Resultats run_020 (Fourier dim=64)**:
- ❌ **Hipòtesi refutada**: més freqüències NO millora
- Peak Sharpness empitjora a 1keV (14.78 → 9.78, −34%) i 100keV (12.96 → 6.59, −49%)
- edep_z_bias empitjora molt a 14.1MeV (+0.01 → +6.77 cm, OK → WRN)
- RI i Spectral W1: idèntics (tots dos BAD)
- dim=32 ja és suficient; dim=64 no ajuda i empitjora

→ [Veure comparativa run_019 vs run_020](compare/compare_020_019.md)
→ [Veure comparativa run_019 vs run_010](compare/compare_019_010.md)
→ [Veure documentació tècnica](basics/Fourier.md) — com funciona Fourier, paràmetres configurables, direccions futures

**run_021** (2026-05-19): Test de **GaussianRandom Fourier Features** per `E_in` (energia incident). En lloc de `Linear(1→64)`, es transforma E_in amb sinus/cosinus de freqüències gaussianes (n_freq=32, scale=8.0). Amb les 7 energies existents per validar el codi abans d'ampliar a 13 energies. Post-run pending.

---

### Fase 7A: Validació d'energies de training — COMPLETADA (2026-05-18)

**Estat**: ✅ **Finalitzada**. run_019 consolidat com a **referència definitiva**.

**Resum**: S'ha validat el model amb run_019 (Fourier dim=32) en 12 energies d'interpolació (4 + 8) fora del training.
El model mostra degradació esperada a energies intermèdies altes (>3MeV), però cap fallada crítica.
Les 7 energies de training actuals són suficients per a MS3.

**Energies de training definitives**: `0.025eV, 1eV, 1keV, 100keV, 1MeV, 5MeV, 14.1MeV` (7E)

**Results eval_001_19** (run_019 + 4 energies intermèdies: 10eV, 10keV, 2MeV, 10MeV):
- ✅ **edep_z_bias OK** a totes les energies (el més important per a MS3)
- ✅ **nhits_ratio** molt precís (1.00 ± 0.01)
- ⚠️ **W1(log_edep) BAD** a 2MeV (0.240) i 10MeV (0.260) — degradació esperada a interpolació alta
- ⚠️ **z_mean_bias WRN** a 2MeV (-1.04cm) i 10MeV (-1.64cm)
- **10keV és la millor interpolació** (W1(log_edep)=0.045, W1(z)=0.415)

→ [Veure eval_001_19 complet](evals/eval_001_19.md)

**Results eval_002_19** (run_019 + 8 energies noves: 0.1eV, 0.5eV, 5eV, 20eV, 50keV, 500keV, 3MeV, 8MeV):
- ✅ **50keV–500keV**: EXCEL·LENT (W1(z)<0.22cm, W1(log_edep)<0.02cm)
- ⚠️ **0.1–20eV**: Acceptable (edep_z_bias<0.4cm, W1(log_edep) marginal)
- ❌ **3–8MeV**: DEGRADACIÓ severa (W1(log_edep)>0.87, W1(z)>1.7cm) — rang fora de cobertura del model actual

→ [Veure eval_002 complet](evals/eval_002.md)

**Decisió (2026-05-18)**: Les 7 energies de training són **suficients per a MS3**.
El model està validat. Degradació a 3–8MeV és coneguda i acceptable per a l'ús actual.
Si es vol millorar el rang MeV en el futur, es prepararà una nova branca amb:
1. **Petits canvis d'arquitectura** relacionats amb `E_inicial` del neutró
2. **Training amb més energies** per donar més informació al model

→ [Planificació de la nova branca](../site/future_branch_plan.md)

### Part V — Voxel Spearman(z, logE) — correlació z↔edep

Més alt = millor correlació entre posició z i energia dipositada. Captura si el model aprèn la distribució espacial de l'energia al llarg de la cascada.

```
Energia     | run_010 | run_013 | run_014 | run_015 | run_016 | run_017 | run_018 | run_019 | run_020
------------------------------------------------------------------------------------------------------------
0.025eV      |   0.469 |   0.463 |   0.425 |   0.457 |   0.466 |   0.463 |   0.460 |   0.472 |   0.390
1eV          |   0.344 |   0.338 |   0.346 |   0.334 |   0.341 |   0.335 |   0.344 |   0.366 |   0.318
1keV         |   0.279 |   0.282 |   0.156 |   0.281 |   0.285 |   0.276 |   0.285 |   0.303 |   0.293
100keV       |   0.091 |   0.092 |   0.180 |   0.010 |   0.090 |   0.089 |   0.012 |   0.092 |   0.154
1MeV         |   0.016 |   0.018 |  -0.029 |   0.063 |   0.017 |   0.018 |   0.065 |   0.002 |   0.096
5MeV         |  -0.027 |  -0.026 |   0.014 |  -0.006 |  -0.006 |  -0.027 |  -0.003 |  -0.024 |   0.023
14.1MeV      |  -0.094 |  -0.094 |  -0.004 |  -0.037 |  -0.040 |  -0.091 |  -0.037 |  -0.097 |  -0.037
```

**Veredicte**: run_019 (Fourier dim=32) millora lleugerament a energies baixes (0.025eV: 0.472 vs 0.469; 1eV: 0.366 vs 0.344; 1keV: 0.303 vs 0.279), consistent amb la millora en captura de ressonàncies. Model B (run_014, bins=8) és el pitjor a totes les energies.

**Nota**: les energies altes (1–14.1 MeV) tenen correlació z↔edep baixa en TOTS els runs — el model perd la informació d'energia al llarg de z en el règim ràpid.

🏆 **run_019 (Fourier dim=32) és la referència definitiva** (consolidada 2026-05-18). Ha substituït run_010 (Linear) com a baseline per a comparacions futures, ja que captura millor l'estructura espectral fina sense degradar cap mètrica.

**Imatges**: mapes voxelitzats E(z,r) disponibles a:
- [Truth](images/truth/) — referència Geant4
- [run_010](images/runs/voxel_emap_010/) — 7 energies
- [run_019](images/runs/voxel_emap_019/) — 7 energies
- [run_020](images/runs/voxel_emap_020/) — 7 energies
- [eval_001](images/runs/voxel_eval/) — 4 energies d'interpolació

---

## Decisions preses

| # | Decisió | Justificació | Runs |
|---|---------|-------------|------|
| 1 | **EPiC-FM + `sum_scale_nmax=True`** | Arquitectura base. `False` → mode collapse | Tots |
| 2 | **Linear embedding continu** (no Model B) | Model B no millora; Linear+condZ interpol·la bé | 013-018, interp_001 |
| 3 | **fs=2 amb condZ** — referència seleccionada | W1(z)=0.076 @1MeV, interpolació validada | run_010 |
| 4 | **Rebutjar edep_beta=2** | Destruïa qualitat sense benefici | 011, 012 |
| 5 | **Rebutjar Model B `n_energy_bins`** | bins=64 ≈ Linear, bins<64 degraduen | 014-018 |
| 6 | **Fourier dim=32 ACCEPTAT com a referència** | Captura pics de ressonància visualment; Peak Sharpness millor; mètriques ≈ Linear | run_019 |
| 7 | **dim=64 Fourier REFUTAT** | Més freqüències empitjora Peak Sharpness i edep_z_bias a altes energies | run_020 |
| 8 | **🔒 Arquitectura tancada (2026-05-17)** | Fourier dim=32 és suficient; més coses no milloraran necessàriament. Validar dades abans de canviar arquitectura. | run_019 |
| 9 | **Fase 7A: Validar energies de training** | Les millores futures (loss espectral, Track 2) es documenten però no s'implementen fins validar que 7 energies no són suficients. | — |
| 10 | **🏆 Consolidar run_019 com a referència definitiva (2026-05-18)** | 7 energies de training suficients per a MS3. Degradació a >3MeV coneguda i acceptable. Nova branca per a millores MeV (E_inicial + més energies). | run_019, eval_001_19, eval_002_19 |
| 11 | **Fourier E_in implementat + run_021 executat (2026-05-19)** | GaussianRFF(n_freq=32, scale=8.0) substituïx MLP Linear per E_in. Configurat amb 7 energies existents per validar codi. Proper pas: ampliar a 13 energies amb run_022. | run_021, future_branch_plan |

[📚 Més detalls](basics/index.md)

## Com funciona aquesta pàgina

Cada run té la seva pàgina amb:
- Motivació i paràmetres de configuració
- Mètriques per energia (W1, bias, peak_r0)
- Gràfics A–F (transforms, z_phys, scatter, perfils)
- Links a runs comparats

Els gràfics es serveixen directament des del repo via GitHub.
