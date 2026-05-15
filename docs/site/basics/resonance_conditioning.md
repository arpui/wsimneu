# Concepte 5b.1 — Resonance awareness via conditioning

**Tags**: #resonance #conditioning #edep #5b1  
**Data**: 2026-05-14  
**Relacionat**: [[ADR_010_resonance_conditioning]], [[energy_invariance_constraint]]

## Resum

Afegir al model una senyal `rho(E_in) ∈ [0, 1]` que indica si l'energia incident
cau dins d'una ressonància neutró-nucli. Aquesta senyal es passa al model com a
condició addicional, juntament amb `log(E_in)`. Quan `rho ≈ 1`, el model sap que
ha de produir hits amb distribució d'edep més dispersa (més variabilitat, no més
pes), capturant els pics de ressonància.

## El problema de fons

El conditioning actual passa `log(E_in)` com a escalar continu. Les ressonàncies
són **discretes** — pics estrets a energies concretes. Un embedding continu no
pot distingir "E=10 eV (continuum)" de "E=10.25 eV (dins ressonància)" perquè
la diferència és mínima respecte a l'escala logarítmica.

```
E_in (continuum): log(E) = 1.0  → model "suaviza" l'espectre
E_in (ressonància): log(E) = 1.025 → model "suaviza" igual, perd el pic
```

## Proposta 5b.1 — Condicionament per ressonància

### Formulació

Afegir una condició addicional `rho(E_in)` al model:

```
Input actual:
  E_in  : (B, 1) → log10(E_MeV) normalitzat
  cond  = cond_mlp(E_in)          # (B, H)

Input amb 5b.1:
  E_in  : (B, 1) → log10(E_MeV) normalitzat
  rho   : (B, 1) ∈ [0, 1] → indicador de ressonància
  cond  = cond_mlp(E_in) + rho_mlp(rho)   # (B, H)
```

On `rho_mlp` és una MLP 2 capes: `1 → H → H` (idèntica estructura a `cond_mlp`).

### Com funciona

- Quan `rho ≈ 0`: `cond ≈ cond_mlp(E_in)` → el model es comporta igual que ara
- Quan `rho ≈ 1`: `cond ≈ cond_mlp(E_in) + rho_mlp(1)` → shift additiu que
  modifica la representació interna del model, fent-lo capaç de generar pics

**Important**: el shift és additiu, no multiplicatiu. No altera el loss. No
amplifica tokens individuals. Només modifica com el model "interpreta" el
context d'energia.

## Model A — `rho` precomputat de taules nuclears

### Fonts de dades

- **ENDF/B-VIII.0**: bases de dades de seccions eficaç per a nuclis (H, C, O, Fe)
- **NJoy**: codi per processar taules ENDF → seccions eficaç punt per punt
- **TENDL**: alternativa a ENDF, amb resolució diferent

### Càlcul de `rho(E_in)`

```python
def compute_rho_E(E_MeV: float, target_nucleus: str = "C12") -> float:
    """
    Calcula rho(E) = σ_approx(E) / σ_max

    σ_approx(E) és un model paramètric de la secció eficaç:
      - Suma de Lorentzians (pic de ressonància)
      - Paràmetres apresos de taules ENDF
      - No cal integrar sobre l'espectre, només punt per punt
    """
    # Exemple: 3 Lorentzians per a C12
    lorentzian(E, E0, Gamma) = 1 / ((E - E0)**2 + (Gamma/2)**2)
    sigma = sum(A_i * lorentzian(E, E0_i, Gamma_i) for i in range(n_peaks))
    return min(1.0, sigma / sigma_max)
```

**Avantatges**:
- Precís: basat en dades experimentals reals
- Transparent: es pot validar visualment
- Reutilitzable per a altres projectes

**Riscos**:
- Depèn de tenir taules ENDF/NJoy a disposició
- Només cobreix els nuclis del dataset (H, C, O, Fe)
- Potser massa detallat (no cal precisió nuclear, només una senyal qualitativa)

### Implementació

1. Precomputar `rho` per a cada energia del dataset multi-E
2. Afegir columna `/rho` a l'HDF5 (com `/E_in`)
3. Actualitzar `data.py` per carregar i batchejar `rho`
4. Afegir `rho_mlp` a `model.py`
5. Actualitzar `cfm.py` per passar `rho` al model

**Fitxers a modificar**:
- `src/genai_mc/neutron_cascade/data.py` — `__getitem__` retorna `"rho": rho_val`, `collate_fn` batcheja
- `src/genai_mc/neutron_cascade/model.py` — `forward(x_t, t, E_in, rho, mask)`
- `src/genai_mc/neutron_cascade/model_epic.py` — idem
- `src/genai_mc/neutron_cascade/cfm.py` — passar `rho` a model.forward
- `scripts/train_neutron_cascade_v5_epic.py` — passar `rho` a cfm_loss_epic
- `scripts/sample_neutron_cascade.py` — calcular `rho` per cada E_in al sampling

## Model B — Embedding discret apès

### Idea

En lloc de passar `rho` com a senyal externa, entrenar el model perquè
aprengui quines energies són "ressonants" durant el training.

```python
# Actual: cond_mlp és un Linear(1, H)
# 5b.1 Model B: cond_mlp és un Embedding sobre bins d'energia
n_bins = 32  # bins log-spaced
self.cond_mlp = nn.Embedding(n_bins, hidden_dim)

# A collate_fn, quantitzar E_in → bin index
E_in_log = torch.log10(E_in_MeV)
E_min, E_max = -5.0, 7.0  # 0.00001 MeV a 10 MeV
bin_idx = ((E_in_log - E_min) / (E_max - E_min) * n_bins).long().clamp(0, n_bins-1)
```

El model aprèn que certs bins (ex: bin per a ~10 eV) generen pics d'edep.

**Avantatges**:
- No cal dades externes (taules ENDF)
- El model aprèn automaticament quines energies són "ressonants"
- Més elegant: el conditioning és purament apès

**Riscos**:
- Menys precís: els bins poden no coincidir exactament amb les ressonàncies
- Pot ser més lent de convergeir: cal que el model "descobreixi" les ressonàncies
- Menys interpretable: no es pot validar visualment

### Implementació Model B

1. Substituir `nn.Linear(1, H)` per `nn.Embedding(n_bins, H)` a `model.py`
2. Quantitzar `E_in` a `bin_idx` a `data.py` (`collate_fn`)
3. Passar `bin_idx` al model (`forward(x_t, t, E_bin_idx, mask)`)
4. Actualitzar `cfm.py` i scripts de training
5. Validar que el model aprèn a generar pics als bins "ressonants"

## Comparació Model A vs Model B

| Aspecte | Model A (rho precomputat) | Model B (embedding discret) |
|---|---|---|
| Precisió | Alta (dades reals) | Mitjana (apres) |
| Dades externes | Sí (ENDF) | No |
| Interpretabilitat | Alta (rho visible) | Mitjana (bins apresos) |
| Implementació | +2 MLPs, +1 columna HDF5 | +1 Embedding, quantització |
| Convergència | Més ràpida (signal clar) | Potser més lent |
| Generalització | Depèn de taules ENDF | Més robust (apres) |

## Paràmetre ajustable: `n_energy_bins`

Aquest és el "knob" principal de 5b.1:

| `n_energy_bins` | Comportament | Ús recomanat |
|---|---|---|
| `None` (default) | Linear(1, H) — comportament legacy | Runs antics sense 5b.1 |
| 8 | 8 bins amples, model senzill | Exploració ràpida |
| 16 | 16 bins, bon punt de partida | **Recomanat per a proves** |
| 32 | 32 bins, més resolució | **Recomanat per a producció** |
| 64 | 64 bins, molt detallat | Només si cal capturar ressonàncies fines |

La quantització mapeja `E_in ∈ [-4.0, 4.0]` (log10 MeV) a bins uniformes.
Energies del dataset:
- 0.025 eV → log10 = -7.6 → E_in_norm ≈ -4.3 (clamped a -4.0, bin 0)
- 14.1 MeV → log10 = 1.15 → E_in_norm ≈ -0.68 (bin proper al centre)

## Recomana

**Model B implementat** (2026-05-14). Comença amb `n_energy_bins=16` per explorar,
puja a 32 si cal més resolució. El model és backward-compatible: runs antics sense
`n_energy_bins` funcionen igual que abans.

## Seqüència d'implementació Model B

**Implementat 2026-05-14**: Model B està completament implementat.

1. **Data pipeline**:
   - `data.py`: `collate_fn` quantitza `E_in` a `E_in_idx`
   - Quantització: `idx = clamp((E_in - E_min) / (E_max - E_min) * (n_bins-1), 0, n_bins-1)`
   - Rang per defecte: E_in ∈ [-4.0, 4.0] (log10 MeV) → cobreix ~1e-4 a ~1e4 MeV

2. **Model**:
   - `model_epic.py`: `self.E_mlp = nn.Embedding(n_bins, E_embed_dim)` si `n_bins > 0`
   - `forward(p_t, g_init, t, E_in, mask)`: `E_emb = E_mlp(E_in.squeeze(-1))`
   - Backward compat: si `n_bins=None` → usa `Linear(1, E_embed_dim)` com abans

3. **CFM loss**:
   - `cfm.py`: `cfm_loss_epic(..., E_in_idx=None)` passa `E_in_idx` al model si no és None
   - `sample_ode_epic(..., E_in_idx=None)` idem

4. **Training**:
   - `train_neutron_cascade_v5_epic.py`: `--n-energy-bins N` (default=None)
   - `--n-energy-bins` es guarda a config.json

5. **Sampling**:
   - `sample_neutron_cascade.py`: quantitza `E_in` a `E_in_idx` segons `model.n_energy_bins`
   - `build_model_from_ckpt`: llegeix `n_energy_bins` de config i construeix el model correctament

6. **Config**:
   - `_run_from_config.py`: passa `--n-energy-bins` des de config al training
   - Config YAML: afegir `"n_energy_bins": 32` al bloc `train`

## Validació

### Indicador de ressonància (RI)

Veure [[ri_analysis]] per a l'anàlisi completa de validesa.

**Actualitzat 2026-05-14**: L'RI té falsos positius per soroll (0–1e-4 MeV)
i poca resolució (30 bins sobre 16 ordres). Valors absoluts no fiables
per decisions quantitatives, però tendències direccionalment correctes.

**Criteris actuals**:
```
RI > 0.80 : Bona reproducció (atenció: falsos positius possibles)
RI > 0.60 : Acceptable (valors >0.60 amb RI actual = molt raro)
RI < 0.60 : Massa suau (la majoria de models actuals cauen aquí)
```

**Valors baseline (2026-05-14)**:
```
run_007: 0.076 (10eV), 0.129 (10keV), 0.080 (2MeV), 0.138 (10MeV)
run_010: 0.057 (10eV), 0.093 (10keV), 0.117 (2MeV), 0.138 (10MeV)
```

**Estratègia de validació 5b.1**:
No confiar només en el valor absolut de RI. Combinar tres mètriques:
1. `W1(log_edep)` — sensibilitat global
2. `RI` — sensibilitat als pics
3. `edep_z_bias_cm` — estructura espacial

Si W1 baixa i RI puja → model millora.
Si W1 baixa però RI no canvia → investigar si el soroll infla l'RI.

**Comportament esperat després de 5b.1**:
- RI puja de ~0.08 a ~0.15–0.25 (no a 0.70+, els valors absoluts són baixos)
- La tendència 10MeV > 10keV > 2MeV > 10eV es manté
- Sense regressió: RI no empitjora a energies altes (5 MeV, 14.1 MeV)

## Relació amb altres propostes

| Proposta | Què fa | Relació amb 5b.1 |
|---|---|---|
| 5b.1 (conditioning) | Diu al model "estàs en ressonància" | Complementari |
| 5b.2 (histogram loss) | Diu al model "l'espectre és massa pla" | Pot ajudar si 5b.1 no suficient |
| FocalR (ADR-006) | Pondera tokens per error | Independent (es podria combinar) |
| Edep-weighted (5a) | Amplifica tokens d'alta edep | ❌ Descartat (distorsiona edep_std) |

## Referències

- ADR-010: [[ADR_010_resonance_conditioning]]
- ADR-006: [[ADR_006_architecture_pivot]]
- Constraint: [[energy_invariance_constraint]]
- Physics: [[cascade_physics]]

---

*Document 2026-05-14. Model B recomanat com a primer pas.*
