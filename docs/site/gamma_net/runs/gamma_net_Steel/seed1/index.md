# GammaNet — Steel (seed=1)

**Data**: 2026-06-19 20:14:10
**Checkpoint**: `runs/gamma_net_Steel/seed1/ckpt_final.pt`
**Dataset**: `data/dataset/gamma_transitions_Steel_all.h5`
**Samples**: 20000 per energia

## Energies

| Energia | Value |
|---------|-------|
| 10keV | 1e4 eV |
| 50keV | 5e4 eV |
| 100keV | 1e5 eV |
| 500keV | 5e5 eV |
| 1MeV | 1e6 eV |
| 2MeV | 2e6 eV |
| 5MeV | 5e6 eV |
| 10MeV | 1e7 eV |
| 14MeV | 1.4e7 eV |

## One-step comparison

Validació atòmica per head: el model prediu quina reacció ocorre
i les variables cinemàtiques (cos_theta, y, delta_d, edep).

![reactions](one_step/all/reactions_combined.png)

![terminate_prob](one_step/all/terminate_prob.png)

## Temps total

642s
