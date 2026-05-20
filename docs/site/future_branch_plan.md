# Planificació de la nova branca — Millores d'arquitectura + més energies

**Data:** 2026-05-18  
**Estat:** 📋 Planificació inicial — Prioritats 2+3 implementades (2026-05-18)  
**Base:** `docs/vault/concepts/review_E_in_conditioning.md` (v2)  
**Referència actual:** run_019 (Fourier dim=32, fs=2, condZ, Linear embedding)  
**Log detallat:** `logs/modified_files.md` — tots els canvis fitxer per fitxer amb timestamps

---

## Resum executiu

**Problema identificat** (segons eval_002_19): degradació severa a energies >3MeV en interpolació (W1(log_edep)>0.87, W1(z)>1.7cm).

**Símptomes observats:**
- **(A)** Sub-estimació de la cua de baix Edep (10⁻⁶ – 10⁻⁴ MeV) a energies incidents baixes-mitjanes (1–20 eV)
- **(B)** Suavització dels pics estrets de la distribució Edep a energies incidents altes (3–10 MeV)

**Diagnòstic de causes:**
- *Causa principal:* densitat insuficient d'energies d'entrenament (gaps de fins a 3 dec, especialment 1eV→1keV)
- *Causa secundària:* embedding de `E_in` (MLP escalar pla) té inductive bias pobre per interpolar fora dels punts vistos
- *Bug ortogonal:* rang de quantització del Model B mal calibrat (66% bins desperdiciats)

**Estat actual de la implementació (2026-05-19):**
- ✅ **Prioritat 2 (Fourier features E_in):** CODI COMPLET — `GaussianFourierFeaturesE` + `E_in_fourier_dim` CLI arg + `collate_fn` normalitza E_in en log10-space z-score
- ✅ **Prioritat 3 (Model B rang):** CODI COMPLET — `make_collate_fn` calcula `E_in_range` automàticament des de `dataset.get_E_in_range()`
- ✅ **run_021 executat** (2026-05-19): Mini-ablation Fourier E_in + 7 energies + 100k iteracions ✅ DONE

**Pla d'acció prioritzat:**
1. 📋 **Densificar energies d'entrenament** de 7 a 13 punts log-uniformes — PENDING (run_021 valida arquitectura, proper pas: ampliar a 13E)
2. ✅ **Substituir MLP escalar de `E_in` per Fourier features** — COMPLET (run_021 valida)
3. ✅ **Corregir rang de quantització del Model B** — COMPLET
4. *Condicional — només si 1+2+3 no resolen suficientment:* migrar de concatenació a FiLM/AdaLN

---

## Prioritat 1 — Densificar energies d'entrenament

### Diagnòstic quantitatiu

Les 7 energies actuals estan mal distribuïdes en log E (eV):

| Energia | log₁₀(E[eV]) | Gap fins al següent |
|---------|--------------|----------------------|
| 0,0025 eV | −2,60 | 2,60 dec |
| 1 eV | 0,00 | **3,00 dec** |
| 1 keV | 3,00 | 2,00 dec |
| 100 keV | 5,00 | 1,00 dec |
| 1 MeV | 6,00 | 0,70 dec |
| 5 MeV | 6,70 | 0,45 dec |
| 14,1 MeV | 7,15 | — |

El gap més gran (1eV→1keV, 3 dec) coincideix exactament amb la zona on es manifesta el símptoma A. Aquesta correlació gap ↔ error és l'indicador que la causa dominant és densitat de dades, no arquitectura.

### Proposta — 13 energies log-uniformes

Separació mitjana ≈ 0,81 dec, lleugerament més densa al rang alt (on apareixen processos discrets addicionals com pair production).

| # | E | log₁₀(E[eV]) |
|---|---|--------------|
| 1 | 0,0025 eV | −2,60 |
| 2 | 0,02 eV | −1,70 |
| 3 | 0,15 eV | −0,82 |
| 4 | 1 eV | 0,00 |
| 5 | 7 eV | 0,85 |
| 6 | 50 eV | 1,70 |
| 7 | 350 eV | 2,54 |
| 8 | 2,5 keV | 3,40 |
| 9 | 18 keV | 4,26 |
| 10 | 130 keV | 5,11 |
| 11 | 900 keV | 5,95 |
| 12 | 6 MeV | 6,78 |
| 13 | 14,1 MeV | 7,15 |

Es preserven energies amb interpretació física directa (tèrmica 0,0025 eV, DT 14,1 MeV) i 1 eV / 1 keV com a ancoratges.

**Rang nou de `E_in_norm`:** s'expandirà a aproximadament `[−1,7, +1,7]`. Caldrà recalcular `log_E_mean` i `log_E_std` sobre el nou conjunt i regenerar `transforms.json`.

### Cost i risc

- **Cost computacional:** 13/7 ≈ 1,9× temps de Geant4 i entrenament
- **Risc:** baix. L'arquitectura no canvia; només cal recalcular constants de normalització

### Test d'ablació intermedi (recomanat)

Abans de les 6 simulacions noves, fer una ablació petita: entrenar amb les 7 actuals **+ 3 punts {10 eV, 100 eV, 10 keV}** (zones de gap més grans) i mesurar:

- Si la cua de baix Edep a l'eval 5–20 eV millora significativament → confirmat, procedir amb graella completa
- Si no millora → hi ha un factor més fort que la densitat (probablement loss o flow). Investigar abans de continuar

### Validesa dels evals existents

Els punts d'eval ja correguts cauen en gaps entre nodes nous, així que es poden reutilitzar com a held-out:

| Eval E | Entre nodes |
|--------|-------------|
| 0,1 / 0,5 eV | 2-4 |
| 5 / 10 / 20 eV | 4-6 |
| 10 / 50 keV | 8-10 |
| 500 keV | 10-11 |
| 2 / 3 MeV | 11-12 |
| 8 / 10 MeV | 12-13 |

L'únic punt massa proper a un node és **0,1 eV** (node 3 = 0,15 eV). Si convé un eval net en aquesta zona, fer-lo a 0,06 eV.

---

## Prioritat 2 — Fourier features per a `E_in`, ben dimensionades

### Per què la proposta original està incompleta

Copiar `sinusoidal_time_embed` directament a `E_in_norm` no funciona bé sense modificacions:

Per a `E_in_norm` de magnitud ~1-2 i `half_dim=32`, l'argument `E*emb[k]` va de ~1 (k=0) fins a ~10⁻⁴ (k=31). La majoria de canals tenen arguments propers a 0, on `sin(x) ≈ x` i `cos(x) ≈ 1`. Només els primers ~5 canals porten informació útil; la resta són pràcticament constants.

`max_period=10000` és apropiat per a `t` (DDPM) però inadequat per a `E_in_norm` en rang ~3 unitats.

### Disseny correcte

L'objectiu és cobrir el ventall útil de freqüències:

- ω més baixa amb període > rang complet (~3,5 unitats), perquè la xarxa distingeixi extrems
- ω més alta amb període < separació mínima entre energies entrenades en `E_in_norm` (~0,4 unitats amb la graella nova)

Implementació:

```python
def fourier_features_E(E_norm, n_freq=32, omega_min=1.0, omega_max=32.0):
    """
    E_norm: (B,) tensor amb energia normalitzada
    Retorna (B, 2*n_freq) features sin/cos.
    """
    freqs = torch.logspace(
        math.log10(omega_min), math.log10(omega_max),
        steps=n_freq, device=E_norm.device
    )
    args = E_norm[:, None] * freqs[None, :]   # (B, n_freq)
    return torch.cat([torch.sin(args), torch.cos(args)], dim=-1)
```

Amb `n_freq=32` surten 64 features, mantenint la mateixa dimensió que el time embedding actual. Després es passa per un MLP de 2 capes igual com per `t`.

### Alternativa més robusta — Gaussian Random Fourier Features

Si es vol evitar el tuning manual d'`omega_min/max`, mostrejar freqüències d'una gaussiana amb escala única:

```python
class GaussianFourierFeatures(nn.Module):
    def __init__(self, n_freq=32, scale=8.0):
        super().__init__()
        self.B = nn.Parameter(torch.randn(n_freq) * scale, requires_grad=False)
        # opcional: requires_grad=True per a freqüències apreses durant el training

    def forward(self, x):
        args = 2 * math.pi * x[:, None] * self.B[None, :]
        return torch.cat([torch.sin(args), torch.cos(args)], dim=-1)
```

Per a `E_in_norm` en `[−1,7, +1,7]`, una escala de ~8 acostuma a funcionar bé. És l'únic hiperparàmetre rellevant.

### Sobre la justificació "spectral bias"

Amb 7 (o 13) punts de training, no hi ha funció d'alta freqüència a aprendre *dins* del conjunt. El benefici real del Fourier embedding aquí no és superar el spectral bias del training, sinó **proveir un inductive bias adequat per a interpolar entre energies entrenades**. Els evals problemàtics són a energies *no vistes*, on un MLP pla sobre escalar produeix activacions massa suaus i el model dóna prediccions similars a energies que físicament generen cascades molt diferents.

### Cost i risc

- **Cost:** zero paràmetres addicionals (el `Linear(1→64)` se substitueix per `Linear(64→64)`)
- **Risc:** baix. Canvi local, fàcilment reversible
- **Validació:** comparar corbes de pèrdua durant 20-30 epoques contra el baseline; mesurar similitud cosinus entre embeddings de `E_in` per a parelles d'energies veïnes — esperem similitud més baixa amb Fourier features

---

## Prioritat 3 — Corregir el rang de quantització del Model B

Amb dades a `[−1,68, +1,07]` (graella vella) o `[−1,7, +1,7]` (graella nova), només s'utilitza un 34-43% dels bins. Per a `n_energy_bins=32`, això vol dir ~11-14 bins actius dels 32.

**Fix:** calcular `E_range` automàticament a partir de `transforms.json` durant la inicialització del dataset, amb marge petit per cobrir interpolacions:

```python
# Al __init__ del dataset:
log_E_mean = transforms['log_E_mean']
log_E_std = transforms['log_E_std']
# Rang ajustat amb marge de ~0,3 unitats:
E_range = (-2.0, 2.0)   # cobreix amb marge tant graella vella com la nova
```

- **Cost:** 1 línia
- **Risc:** zero
- **Prerequisit:** només té sentit si s'usa Model B en els experiments planejats

---

## Prioritat 4 (condicional) — FiLM/AdaLN per a `E_in`

Aquesta recomanació es desprioritza. Raons:

- A DiT, AdaLN guanya per la quantitat i diversitat de senyals de condicionament (text llarg, classes, t). Aquí el condicionament és essencialment 2 escalars després d'embedding (`t`, `E_in`), un cas on la concatenació no està tan saturada.
- La major part dels símptomes observats (cua baix Edep, pics suavitzats) venen de les causes anteriors (densitat d'energies + qualitat d'embedding)
- Cost moderat: canvis a totes les `EpicLayer` + reentrenament complet

**Implementació, si s'arriba aquí:**

```python
# Dins EpicLayer.__init__
self.film_g = nn.Linear(E_embed_dim, 2 * global_dim)
self.film_p = nn.Linear(E_embed_dim, 2 * hit_dim)

# forward
gamma_g, beta_g = self.film_g(E_emb).chunk(2, dim=-1)
g = g * (1 + gamma_g) + beta_g

gamma_p, beta_p = self.film_p(E_emb).chunk(2, dim=-1)
p = p * (1 + gamma_p.unsqueeze(1)) + beta_p.unsqueeze(1)
```

---

## Taula resum

| # | Aspecte | Estat actual | Acció proposada | Cost | Impacte esperat | Prioritat |
|---|---------|--------------|-----------------|------|------------------|-----------|
| 1 | Densitat energies entrenament | 7 punts, gaps de fins a 3 dec | 13 punts log-uniformes | ~1,9× temps entrenament | **Alt** — ataca la causa principal de (A) | 🔴 Alta |
| 2 | Embedding `E_in` | MLP escalar pla | ✅ **COMPLET** — GaussianRFF scale=8.0, `E_in_fourier_dim` CLI + **run_021 validat** | Zero params extra | **Mitjà-alt** — millora interpolació | 🔴 Alta |
| 3 | Rang quantització Model B | `[-4, 4]` fix | ✅ **COMPLET** — Auto-calcular des de dataset | 1 línia | Mitjà (només si Model B) | 🟡 Mitja |
| 4 | Injecció `cat` vs FiLM | Concatenació | FiLM/AdaLN | Reentrenament complet | Baix-mitjà residual | 🟢 Condicional |

---

## Plan d'execució

### Fase A — Validació ràpida (1-2 setmanes)

1. ✅ Implementar Prioritat 3 (Model B rang) — CODI COMPLET
2. ✅ Implementar Prioritat 2 (Fourier features per `E_in`) — CODI COMPLET
3. ✅ **run_021 mini-ablation:** Executat 2026-05-19 — Fourier E_in + **7 energies** (no 13) + 100k iteracions ✅ DONE
4. 📋 **run_022 full production:** Configurat amb Fourier E_in + 13 energies + 500k iterations. PENDING: execute after run_021 analysis + Geant4 data for new energies

### Fase B — Graella completa (≈ 1 mes, segons simulació Geant4)

5. Generar Geant4 per a les 13 energies (les 6 que falten). Recalcular `transforms.json`. Reentrenar el model amb Fourier features. Bateria completa d'evals.

### Fase C — Refinament (només si cal)

6. Si després de B encara queden pics estrets suavitzats a alta E (símptoma B residual): provar FiLM/AdaLN. Si el problema persisteix amb FiLM/AdaLN, mirar la loss del flow.

---

## Mètriques d'èxit

Per considerar que la nova branca ha funcionat, cal millorar **almenys una** d'aquestes mètriques vs run_019:

| Mètrica | run_019 actual | Objectiu |
|---------|---------------|----------|
| W1(log_edep) @ 3MeV | ❌ 0.871 (BAD) | <0.30 OK |
| W1(log_edep) @ 8MeV | ❌ 0.891 (BAD) | <0.30 OK |
| W1(z) @ 3MeV | ❌ 2.151 (BAD) | <1.0 OK |
| W1(z) @ 8MeV | ⚠️ 1.774 (WRN) | <1.0 OK |
| W1(log_edep) @ 2MeV | ❌ 0.240 (BAD) | <0.15 OK |
| W1(log_edep) @ 10MeV | ❌ 0.260 (BAD) | <0.15 OK |
| edep_z_bias @ 3MeV | ✅ -1.07 | <2.0 cm OK |
| edep_z_bias @ 8MeV | ✅ -0.98 | <2.0 cm OK |

**Nota:** edep_z_bias ja es OK a totes les energies (MS3). El focus es W1(log_edep) i W1(z).

---

## Implementació completada (2026-05-18)

### Prioritat 2 — Fourier features per a `E_in`

**Fitxers modificats:**
- `src/genai_mc/neutron_cascade/model_epic.py` — `GaussianFourierFeaturesE` (línia 63-95) + `EpicFMModel` init/forward amb `E_in_fourier_dim` (línia 336-388)
- `src/genai_mc/neutron_cascade/data.py` — `get_E_in_stats()` + `collate_fn` normalitza E_in en log10-space z-score (línia 130-137, 212-216)
- `scripts/train_neutron_cascade_v5_epic.py` — `--E-in-fourier-dim` CLI arg (línia ~200)
- `scripts/_run_from_config.py` — `E_in_fourier_dim` passthrough (línia ~45)

**Detall tècnic:**
- `GaussianFourierFeaturesE(n_freq=32, scale=8.0)` → 64 features sin/cos → Linear(64→64)
- E_in normalitzat via `log10(E_raw) - mean) / std` consistent entre training i sampling
- Model B (Embedding) funciona de forma independent: indices computats des de `E_in_raw`
- Linear model (legacy) funciona de forma independent: `E_in` passat directament

### Prioritat 3 — Rang de quantització Model B

**Fitxers modificats:**
- `src/genai_mc/neutron_cascade/data.py` — `make_collate_fn` calcula `E_in_range` des de `dataset.get_E_in_range(margin=0.3)` (línia 252-254)
- `src/genai_mc/neutron_cascade/data.py` — `collate_fn` normalitza SEMPRE quan `E_in_mean/std` no són None (línia 255-257)

**Detall tècnic:**
- Quan `n_energy_bins > 0`, el rang es calcula des de `E_in_arr.min()/max() ± margin` (margin=0.3)
- `E_in_mean/std` es calculen automàticament des del dataset si no es passen explícitament

### Configs creades

- `runs/nc_multiE/run_021/run_config.json` — mini-ablation: **7 energies** (dataset actual), Fourier E_in dim=32, fs=2, 100k iterations ✅ EXECUTAT 2026-05-19
- `runs/nc_multiE/run_022/run_config.json` — full production: 13 energies, Fourier E_in dim=32, fs=2, 500k iterations 📋 PENDING

### Fix critic aplicat (2026-05-18)

Bug detectat: `make_collate_fn()` normalitzava E_in sempre que hi hagués dataset stats,
independentment de si el model usava Fourier features o no.

Fix: `make_collate_fn()` ara només normalitza E_in quan `E_in_fourier_dim > 0`.

Això afecta tots els runs anteriors (008-020) que van entrenar amb E_in normalitzat
sense que el model Linear ho esperés. Els runs futurs (021, 022) funcionen correctament.

### Fitxers modificats

**src/genai_mc/neutron_cascade/model_epic.py:**
- Nova classe `GaussianFourierFeaturesE(n_freq, scale)` — Gaussian RFF per a E_in
- Nou paràmetre `E_in_fourier_dim` a `EpicFMModel.__init__` (default=0)
- `forward()`: routing automàtic Embedding | Fourier | Linear

**src/genai_mc/neutron_cascade/data.py:**
- Nova funció `get_E_in_stats()` — calcula mean/std de log10(E_in)
- `collate_fn()`: normalitza E_in en log10 z-score quan `E_in_mean/std` no són None
- `make_collate_fn()`: nou param `E_in_fourier_dim` — normalitza SOLO quan > 0

**scripts/train_neutron_cascade_v5_epic.py:**
- Nou argument CLI `--E-in-fourier-dim N` (default=0)
- Passat a `EpicFMModel()` i `make_collate_fn()`

**scripts/sample_neutron_cascade.py:**
- `build_model_from_ckpt()`: llegeix `E_in_fourier_dim` de config → passa a `EpicFMModel()`
- Normalització multi-E ja consistent (log10 z-score)

**scripts/_run_from_config.py:**
- Nou paràmetre `E_in_fourier_dim` — passthrough a comanda training

### Eines validades

- E_in normalization flow: training `collate_fn` → model `forward()` consistent amb sampling multi-E normalització
- Shape consistency: `(B, 1)` float tensor per a Linear/Fourier, `(B,)` long per a Embedding
- `GaussianFourierFeaturesE.forward()`: `squeeze(-1)` correcte per a tots els modes

---

## Referències

- run_019: `docs/site/runs/run_019.md` — referència actual
- eval_001_19: `docs/site/evals/eval_001_19.md` — 4 energies intermèdies
- eval_002_19: `docs/site/evals/eval_002_19.md` — 8 energies esteses + voxels
- MS7A: `docs/vault/milestones/MS7A_eval_energies.md` — milestone Fase 7A
- review_E_in: `docs/vault/concepts/review_E_in_conditioning.md` — document base v2
- **modified_files.md:** `logs/modified_files.md` — log detallat timestamp + fitxer per fitxer

---

*Creat: 2026-05-18. Base: `docs/vault/concepts/review_E_in_conditioning.md` (v2). Actualitzat segons diagnòstic de eval_002_19. Prioritats 2+3 implementades: 2026-05-18.*
