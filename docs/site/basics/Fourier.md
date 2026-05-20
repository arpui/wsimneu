# Fourier Feature Embedding per a edep

## Funcionament

En lloc de passar `edep` directament (`float`) al primer layer de la xarxa, es transforma a un vector de sinus/cosinus de freqüències diferents:

```
edep (1 valor) → FourierFeatureEmbedding → vector de N valors
```

La transformació és:

```
x → [sin(2π·B·x), cos(2π·B·x)]
```

on `B` és una matriu de freqüències aleatòries de shape `(1, N/2)` inicialitzada amb `torch.randn(1, N/2)` → normal(0,1).

### Desglossament

- **in_dim = 1** (només edep)
- **out_dim** → `half = out_dim // 2` freqüències aleatòries
- Cada freqüència `B[0, j]` s'aplica a `x`: calcula `sin(2π·B_j·x)` i `cos(2π·B_j·x)`
- Resultat: `half` sinus + `half` cosinus = `out_dim` dimensions

## Implementació

Classe a `src/genai_mc/neutron_cascade/model_epic.py`:

```python
class FourierFeatureEmbedding(nn.Module):
    def __init__(self, in_dim: int = 1, out_dim: int = 32, B: torch.Tensor | None = None):
        self.out_dim = out_dim
        half = out_dim // 2
        if B is None:
            B = torch.randn(in_dim, half)
        self.register_buffer("B", B)

    def forward(self, x: torch.Tensor) -> torch.Tensor:
        args = 2 * math.pi * (x @ self.B)
        return torch.cat([torch.sin(args), torch.cos(args)], dim=-1)
```

### On s'integra

Al `EpicFMModel`:

```python
# A run_config.json → train.edep_fourier_dim
if edep_fourier_dim > 0:
    self.edep_fourier = FourierFeatureEmbedding(in_dim=1, out_dim=edep_fourier_dim)
    # hit_dim intern: r(1) + z(1) + t(1) + edep_fourier(edep_fourier_dim)
    self._internal_hit_dim = 3 + edep_fourier_dim
else:
    self.edep_fourier = None

# Forward: en lloc de passar edep directe, passar Fourier features
if self.edep_fourier is not None:
    edep_ff = self.edep_fourier(edep)  # (B, N, edep_fourier_dim)
    p_in = torch.cat([spatial, edep_ff], dim=-1)
```

### Cost computacional

- `edep_fourier_dim=32` → `+7,936 paràmetres` al primer layer (`hit_in`: `Linear(3+32, 256)`)
- Sense Fourier: 2,133,252 params → Amb Fourier: 2,141,188 params
- La inferència és gairebé idèntica (només el primer layer és lleugerament més gran)

## Motivació: Spectral Bias

Les MLPs tenen un **spectral bias** documentat: converxen ràpidament a components de baixa freqüència i costa aprendre estructures fines (pics en `edep`). Els pics de ressonància són alta freqüència en l'espai d'entrada.

Els Fourier features "eleven" la representació a un espai on els pics es tornen més lineals per a la xarxa, permetent capturar estructures fines amb menys iteracions de training.

Aquesta tècnica s'ha demostrat efectiva a NeRF, SIREN, i models de scoring (Cohen et al. 2021).

## Paràmetres configurables

### `edep_fourier_dim` (configurable)

| Paràmetre | Valor actual | Què fa |
|-----------|-------------|--------|
| `edep_fourier_dim` | **32** | Sortida del Fourier embedding. Més alt = més freqüències = més capacitat espectral = més paràmetres |

Valors possibles:
- **16**: Menys freqüències → menys paràmetres (+4k) → més ràpid, menys capacitat
- **32**: Actual, equilibrat (+8k params) — **RECOMANAT**
- **64**: Més freqüències → més capacitat espectral (+16k params) — **REFUTAT** (run_020)
- **128**: Alta capacitat (+32k params) — probablement excessiu

> **Nota**: `edep_fourier_dim=64` es va provar (run_020) i **no millora** dim=32. Empitjora a 1keV (Peak Sharpness 9.78 vs 14.78) i 100keV (6.59 vs 12.96).

### Paràmetres no configurables (hardcoded)

| Paràmetre | Valor actual | Què podria canviar |
|-----------|-------------|-------------------|
| `in_dim` | **1** | Podria ser 4 (r,z,t,edep) per aplicar Fourier a tots els canals |
| `B ~ N(0,1)` | **Aleatori per training** | Podria ser fixa/reprodueïble o usar freqüències log-spaced determinístiques |
| `2π·B·x` | **Constant** | Factor 2π fix. Podria ajustar-se (escalar B per controlar el rang de freqüències) |

## Direccions futures

### Phase 6: Loss espectral / weighting adequat

Les mètriques agregades (W1, RI, edep_z_bias) són ≈idèntiques entre Linear i Fourier, però el Peak Sharpness mostra que Fourier captura pics més definits a 1keV–1MeV. Per treure més profit d'aquesta millora:

1. **Loss espectral**: Penalitzar la diferència entre histogrames (no només W1 puntual)
   - Soft histogram loss: comparar histogrames log-spaced de edep gen vs truth
   - RI-weighted loss: donar més pes als bins amb pics de ressonància
   - Peak-aware loss: penalitzar específicament la subestimació de pics

2. **Importance weighting**: Donar més pes a les mostres amb edep en pics de ressonància
   - Ponderar events amb edep en rangs de ressonància (eV–keV)
   - Auto-weighting: ajustar pesos durant training basat en la distribució actual

3. **Fourier + loss espectral**: Combinar Fourier features amb un loss que premi la captura de pics

### Altres direccions Fourier

1. **Freqüències log-spaced determinístiques**: En lloc de `randn(1,16)`, usar freqüències logarítmiques entre edep min i max → millor cobertura de l'espai d'edep
2. **Escalar B**: Multiplicar B per un factor `scale` que s'ajusti a la distribució real d'edep (actualment B és aleatori sense adaptar-se als dades)
3. **Fourier sobre tots els canals**: `in_dim=4` per aplicar Fourier a r, z, t, edep (actualment només edep)
4. ~~`edep_fourier_dim=64`~~: **REFUTAT** (run_020 — 2026-05-17)

## Resultats

### run_019 (Fourier dim=32) vs run_010 (Linear)
Veure [compare_019_010](compare_019_010.md) per la comparativa quantitativa.

**Resum**: Fourier dim=32 millora visualment la captura de pics de ressonància (Peak Sharpness +1keV–1MeV), però no millora les mètriques agregades (W1, RI).

### run_020 (Fourier dim=64) vs run_019 (Fourier dim=32)
Veure [spectral_metrics_020_019](spectral_metrics_020_019/) i [compare_020_019](compare_020_019.md).

**Resum: HIPÒTESI REFUTADA** — `edep_fourier_dim=64` NO millora dim=32:

| Mètrica | run_019 (dim32) | run_020 (dim64) | Conclusió |
|---------|-----------------|-----------------|-----------|
| Peak Sharpness (1keV) | **14.78** | 9.78 | Empitjora |
| Peak Sharpness (100keV) | **12.96** | 6.59 | Empitjora |
| Peak Sharpness (5MeV) | 53.74 | **56.48** | Millora lleugera |
| Peak Sharpness (1eV) | 3.82 | **4.68** | Millora lleugera |
| RI (totes energies) | BAD (~0.08) | BAD (~0.09) | Idèntic |
| Spectral W1 (keV) | BAD (~0.83-1.05) | BAD (~0.83-1.05) | Idèntic |
| edep_z_bias (14.1MeV) | +0.01 **OK** | +6.77 **WRN** | Empitjora molt |
| W1(log_edep) (1eV) | WRN 0.106 | **0.041 OK** | Millora |

**Conclusió**: El augment de freqüències Fourier no ajuda a capturar millor els pics. El problema no és la representació (Fourier dim=32 ja és suficient), sinó el **loss de training** que no premia la captura de pics. La direcció futura és **Phase 6: loss espectral / importance weighting**.
