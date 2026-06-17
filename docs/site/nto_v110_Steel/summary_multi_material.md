# V11.0 Multi-Material — Resum executiu

**Data:** 2026-06-17 (actualitzat)

## Estat dels 7 materials

| Material | Dataset | Training | Compare | One-step | RF | Conclusions | Estat |
|----------|---------|----------|---------|----------|-----|-------------|-------|
| **Steel** | ✅ 36.5M steps | ✅ 30 epochs (-4.25) | ✅ | ✅ | ✅ | ✅ | 🟢 Complet |
| **Paraffin** | ✅ 93M steps | ✅ 10 epochs (0.92) | ✅ | ✅ | ✅ | ✅ | 🟢 Complet |
| **Graphite** | ✅ 60M steps | ✅ 30 epochs (-6.10) | ✅ | ✅ | ✅ | ✅ | 🟢 Complet |
| **D2O** | ✅ 29.2M steps | ✅ 30 epochs (-0.47) | ✅ | ✅ | ✅ | ✅ | 🟢 Complet |
| **DPE** | ✅ 47.3M steps | ✅ 30 epochs (0.25) | ✅ | ✅ | ✅ | ✅ | 🟢 Complet |
| **AirHumid** | ✅ 167K steps | ✅ 30 epochs (4.67) | ✅ | ✅ | ✅ | ✅ | 🟡 Complet (pocs dades) |
| **H2O** | ❌ HDF5 corrupte | — | — | — | — | — | 🔴 Pendents |

## Canvis al codi

### reaction_catalog.py
- **Abans**: 49 reaccions (H, C, He + Steel elements)
- **Ara**: 59 reaccions (+ O, Ar, D)

### material.py
- **Abans**: 5 materials (PE, He3, Al, Air, Steel)
- **Ara**: 11 materials (+ H2O, AirHumid, Paraffin, Graphite, D2O, DPE)

## Resultats per material

### Steel
- Hits ratio: 1.04-1.28 a energies baixes
- Cosθ W1: < 0.01 a totes les energies
- Problema: overfitting a 1 MeV (hits_ratio=13.88)

### Paraffin
- Hits ratio: 0.95-1.00 a energies < 1 MeV (excel·lent)
- Cosθ W1: < 0.015 a totes les energies
- Molt similar a PE (quimicament equivalent)

### Graphite
- Hits ratio: ~3x (genera massa hits)
- Cosθ W1: 0.02-0.05 a energies baixes
- Degrada molt a 6-14 MeV

### D2O
- Hits ratio: 1.47-1.81 a energies < 1 MeV
- Cosθ W1: 0.03-0.04 a energies < 1 MeV
- Degrada a 6-14 MeV (hits_ratio=4.1)

### DPE
- Hits ratio: 0.94-1.18 a energies < 1 MeV (excel·lent)
- Cosθ W1: < 0.02 a energies < 1 MeV
- Millor que D2O, comparable a PE

### AirHumid
- Dataset massa petit (167K steps)
- Totes les mètriques són dolentes
- Calen 100M events en lloc de 1M

### H2O
- Fitxer HDF5 corrupte (filter error)
- Cal regenerar amb simg4

## Directors de sortida

```
docs/site/nto/runs/
├── nto_v110_Steel/seed1/        ← all/, one_step/, reaction_fractions
├── nto_v110_Paraffin/seed1/     ← all/, one_step/, reaction_fractions
├── nto_v110_Graphite/seed1/     ← all/, one_step/, reaction_fractions
├── nto_v110_D2O/seed1/          ← all/, one_step/, reaction_fractions
├── nto_v110_DPE/seed1/          ← all/, one_step/, reaction_fractions
├── nto_v110_AirHumid/seed1/     ← all/, one_step/, reaction_fractions
└── nto_v110_Steel/conclusions.md
```

## Pròxims passos

1. **H2O**: Regenerar fitxer HDF5 amb simg4
2. **AirHumid**: Generar 100M events per tenir dataset suficient
3. **Tots**: Implementar weighted loss per bin energètic
4. **Steel/Graphite**: Afegir isòtops naturals al catàleg (~10% UNKNOWN)
5. **D2O/DPE**: Usar max-steps=50 per evitar rollouts infinits

## Notes tècniques

- **max-steps necessari**: D2O i DPE tenen cascades llargues (deuteri menys captura → més steps). Sense max-steps el sampling penja.
- **Deuteri**: 3 reaccions afegides (elastic, (n,2n), (n,γ)). El (n,2n) és important per multiplicació neutrònica.
