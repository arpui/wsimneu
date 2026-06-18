# Steel — Isòtops que falten al catàleg

## Resum

El catàleg actual de V11.0 té 59 reaccions per als elements principals de Steel
(Fe-56, Cr-52, Ni-58, C, Mn, Si, P, S, N, O, Ar, D, H, He).

**Problema**: Geant4 simula **isòtops naturals**, no només els més abundants.
Això resulta en ~10% de hits UNKNOWN (no al catàleg).

## Iron (Fe) — Isòtops que falten

| # | ZAID | Isòtop | Abundància | MT | Reacció | Hits | % total | Prioritat |
|---|------|--------|-----------|-----|---------|------|---------|-----------|
| 1 | 26054 | ⁵⁴Fe | 5.8% | 2 | elastic | 864,129 | 2.36% | 🔴 ALTA |
| 2 | 26054 | ⁵⁴Fe | 5.8% | 102 | (n,γ) | 19,355 | 0.05% | 🟡 |
| 3 | 26057 | ⁵⁷Fe | 2.1% | 2 | elastic | 256,340 | 0.70% | 🟡 MITJANA |
| 4 | 26057 | ⁵⁷Fe | 2.1% | 4 | (n,n') | 20,963 | 0.06% | 🟢 |
| 5 | 26057 | ⁵⁷Fe | 2.1% | 102 | (n,γ) | 7,023 | 0.02% | 🟢 |
| 6 | 26058 | ⁵⁸Fe | 0.28% | 2 | elastic | 66,981 | 0.18% | 🟢 BAIXA |
| 7 | 26058 | ⁵⁸Fe | 0.28% | 102 | (n,γ) | 945 | <0.01% | 🟢 |

**Total Fe UNKNOWN**: ~1,235,736 hits (**3.38%** del total)

**Nota**: ⁵⁶Fe (91.75% natural) ja està al catàleg amb 6 reaccions.

## Altres isòtops que falten

| # | ZAID | Isòtop | MT | Reacció | Hits | % total | Prioritat |
|---|------|--------|-----|---------|------|---------|-----------|
| 1 | 24053 | ⁵³Cr | 2 | elastic | 1,351,435 | 3.70% | 🔴 ALTA |
| 2 | 24050 | ⁵⁰Cr | 2 | elastic | 449,217 | 1.23% | 🟡 MITJANA |
| 3 | 28060 | ⁶⁰Ni | 2 | elastic | 383,985 | 1.05% | 🟡 MITJANA |
| 4 | 28062 | ⁶²Ni | 2 | elastic | 339,552 | 0.93% | 🟡 MITJANA |
| 5 | -1 | desconegut | -1 | procés? | 278,531 | 0.76% | ⚠️ INVESTIGAR |
| 6 | 24054 | ⁵⁴Cr | 2 | elastic | 60,952 | 0.17% | 🟢 |
| 7 | 28061 | ⁶¹Ni | 2 | elastic | 30,569 | 0.08% | 🟢 |
| 8 | 28064 | ⁶⁴Ni | 2 | elastic | 14,574 | 0.04% | 🟢 |

## Procés per afegir isòtops

**NO cal re-executar Geant4** — el truth HDF5 ja té ZAID/MT a columnes 16-17.

El procés és:
1. Afegir reaccions al `reaction_catalog.py`
2. Re-executar `MATERIAL=Steel bash scripts/build_dataset_v110.sh`
   → regenera `transitions_v110_Steel_all.h5` (ara amb els nous reaction_idx)
3. Re-entrenar el model
4. Re-executar compare + one_step

## Proposta: Afegir top-5 isòtops (estalvia ~9.3% UNKNOWN)

Per reduir els UNKNOWN de ~10% a ~1%, caldria afegir:

```python
# ⁵⁴Fe (26054) — 2.41% dels hits
ReactionSpec(26054, MT_ELASTIC, "54Fe", "54Fe_elastic",
             0.0, "⁵⁴Fe(n,n) — scatter elàstic", ("recoil_54Fe",)),
ReactionSpec(26054, MT_GAMMA_CAP, "54Fe", "54Fe(n,gamma)55Fe",
             10.0, "⁵⁴Fe(n,γ)⁵⁵Fe — captura radiativa", ("gamma_10MeV",)),

# ⁵⁷Fe (26057) — 0.78% dels hits
ReactionSpec(26057, MT_ELASTIC, "57Fe", "57Fe_elastic",
             0.0, "⁵⁷Fe(n,n) — scatter elàstic", ("recoil_57Fe",)),
ReactionSpec(26057, MT_NN_PRIME, "57Fe", "57Fe(n,n')57Fe*",
             -7.0, "⁵⁷Fe(n,n')⁵⁷Fe* — inelàstic", ("neutron_emit",)),
ReactionSpec(26057, MT_GAMMA_CAP, "57Fe", "57Fe(n,gamma)58Fe",
             10.0, "⁵⁷Fe(n,γ)⁵⁸Fe — captura radiativa", ("gamma_10MeV",)),

# ⁵⁸Fe (26058) — 0.19% dels hits
ReactionSpec(26058, MT_ELASTIC, "58Fe", "58Fe_elastic",
             0.0, "⁵⁸Fe(n,n) — scatter elàstic", ("recoil_58Fe",)),
ReactionSpec(26058, MT_GAMMA_CAP, "58Fe", "58Fe(n,gamma)59Fe",
             10.0, "⁵⁸Fe(n,γ)⁵⁹Fe — captura radiativa", ("gamma_10MeV",)),
```

**Impacte**: N_REACTIONS passaria de 59 a 66. El model veuria ~3.4% més dades de Fe.

## Alternativa: no afegir isòtops

Si no volem augmentar N_REACTIONS:
1. Acceptar el ~10% UNKNOWN (el model ignora aquests hits)
2. Els UNKNOWN són principalment elàstics → el model ja aprèn l'elàstic dels isòtops principals
3. L'impacte en qualitat és menor que la distribució d'energia o el weighted loss

## Recomanació

**Per ara**: No afegir isòtops. El 10% UNKNOWN és acceptable perquè:
- Són principalment elàstics (el model ja aprèn aquesta reacció)
- L'impacte en les mètriques és menor que altres factors
- Augmentar N_REACTIONS augmenta la mida del model

**Després**: Si les mètriques de Steel no milloren amb altres optimizations
(weighted loss, més epochs), considerar afegir els top-5 isòtops.

**Quan s'afegeixin**: No cal re-executar Geant4. Només:
1. Afegir al `reaction_catalog.py`
2. Re-executar `build_dataset_v110.sh`
3. Re-entrenar
4. Re-executar compare + one_step
