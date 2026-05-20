# Estratègies per millorar la predicció de pics en el model EPIC-FM

## Context

Reproducció de simulacions Monte Carlo (Geant4) amb una xarxa neuronal. Les zones suaus de l'espectre es prediuen bé, però els pics/ressonàncies queden suavitzats o desplaçats. L'objectiu és millorar-ho **sense passar la taula de ressonàncies** — només amb tècniques autosuficients sobre les dades, el loss o l'arquitectura.

## Diagnòstic del problema

L'MSE pondera per igual cada bin. Com que els pics ocupen poca àrea total, contribueixen poc al loss i la xarxa "prefereix" predir-los a la baixa: és energèticament més rendible encertar el fons que arriscar-se als pics. Cal trencar aquesta asimetria.

---

## 1. Loss ponderat per la pròpia estructura del target

Probablement el canvi més rendible. No requereix res extern: el pes de cada bin surt del propi target.

### Pes per derivada / curvatura

```
w_i = 1 + α · |y''_i|
```

o, alternativament:

```
w_i = 1 + α · |Δy_i| / σ_local
```

Els bins amb segona derivada gran (pics, valls, vores) pesen més. El càlcul és sobre el target real, no la predicció — no hi ha cap risc d'inestabilitat.

### Pes per prominència local

```
w_i = 1 + α · max(0, y_i − y_baseline_i)
```

amb `y_baseline` calculat com a mediana mòbil o filtrat morfològic. Tot el que sobresurt del fons rep més pes automàticament.

### Focal loss per regressió

```
L = |y − ŷ|^γ     amb γ ∈ [1, 2]
```

Els errors grans (típicament als pics) pesen desproporcionadament més.

---

## 2. Loss en domini espectral / multi-escala

Afegir a l'MSE habitual un terme que compari la FFT (o wavelets) de predicció i target. Penalitza específicament la pèrdua d'estructura fina i és invariant a petits desplaçaments.

```
L = L_MSE + λ_1 · ||FFT(y) − FFT(ŷ)|| + λ_2 · L_multi_escala
```

- **L_multi_escala**: MSE entre versions del senyal filtrades a diferents amples (downsampling per piràmide).
- Es pot afegir un terme **TV invers** que penalitzi excés de suavitat on el target té variabilitat real.

---

## 3. Loss heterocedàstic (la xarxa aprèn on equivocar-se costa més)

La xarxa treu també un mapa de σ(x) i s'utilitza un Gaussian NLL:

```
L = (y − ŷ)² / (2σ²) + ½ · log σ²
```

La xarxa aprèn sola que als pics no es pot permetre errors grans.

**Variant Poisson** (recomanada si els targets són counts de Monte Carlo):

```
L = ŷ − y · log ŷ
```

És estadísticament correcte per a comptatges i pondera implícitament pels pics (N alt).

---

## 4. Capa de sortida parametritzada (la més "física")

Idea més potent per construir un veritable **atractor cap a forma de ressonància**. En lloc de predir bin a bin lliurement, la xarxa genera paràmetres d'una descomposició:

```
ŷ(E) = baseline_suau(E) + Σₖ Lorentzian(E; centreₖ, amplada_k, amplitudₖ)
```

- La xarxa aprèn a treure N tripletes (centre, amplada, amplitud) més un background suau.
- Forces **estructuralment** la forma correcta sense passar cap taula — la xarxa descobreix sola on cal posar Lorentzians/Breit-Wigners per minimitzar l'error.
- N es fixa amb marge (per exemple 20-50 components), amb penalitat L1 sobre `amplitud_k` perquè les sobrants es "desactivin".
- El gradient pels paràmetres és exacte i estable.

### Variant híbrida (recomanada)

La xarxa treu `(ŷ_lliure, paràmetres_lorentzians)` i la sortida final és la suma. Manté flexibilitat però té un camí dedicat per a les ressonàncies.

---

## 5. Cap auxiliar predictiu (multi-task)

Segona sortida que prediu un **mapa de "peakness"** derivat automàticament del target:

```
p_i = max(0, y_i − filtre_morfològic(y))
```

o bé `p_i = |y''_i|` normalitzat. Aquest pseudo-label es genera en temps de training a partir del propi target. Tasques auxiliars d'aquest tipus regularitzen molt bé i obliguen la xarxa a representar internament l'estructura de pics. A inferència es descarta aquesta sortida (o s'utilitza com a diagnòstic).

---

## 6. Hard example mining temporal

Cada cert nombre d'èpoques:

1. Calcular l'error per bin sobre el train set.
2. Identificar les regions on l'error és més alt (tendiran a ser els pics).
3. Oversample-les o multiplicar el seu pes al loss.

És adaptatiu i no necessita coneixement extern.

---

## 7. Arquitectura: atenció i receptive field

- Si l'arquitectura actual és convolucional, afegir un bloc d'**atenció** (self-attention sobre la dimensió espectral) ajuda molt amb estructures locals d'alta freqüència.
- Revisar el **receptive field**:
  - Massa gran → la xarxa promitja massa.
  - Massa petit → no captura l'amplada de la ressonància.
- Una **U-Net** amb skip connections preserva millor el detall fi que una xarxa purament feed-forward.

---

## Ordre pràctic recomanat

| Pas | Acció | Cost | Guany esperat |
|-----|-------|------|---------------|
| 1 | Loss ponderat per curvatura | 1 línia de codi | ~60% de la millora |
| 2 | Terme FFT al loss | Poques línies | Millora addicional en estructura fina |
| 3 | Capa parametritzada Lorentziana | Refactor moderat | Salt qualitatiu |
| 4 | Cap auxiliar + hard mining | Regularització | Estabilitza i polit final |

---

## Diagnòstic previ (important)

Abans de canviar res, fer un **baseline diagnòstic**: mirar si l'error als pics és sistemàtic o estocàstic.

- **Sistemàtic** (subestima amplitud de manera consistent) → problema de loss/arquitectura. Les estratègies anteriors funcionen.
- **Estocàstic** (varia bin a bin sense patró) → possiblement falta de capacitat o de dades a la zona de ressonàncies. Cal revisar primer la **cobertura del dataset**.

1. Loss ponderat per curvatura / prominència
Veredicte: ✅ És el pas 1 correcte. Cost mínim (~3-4 línies), sense arriscar res.
Però: el pes w_i s'aplica sobre el target, no sobre la distribució de hits que genera el model. El problema de fons és que el model genera ~50-200 hits per event, i l'histograma final és una acumulació estocàstica. Ponderar bins individuals del target no necessàriament es tradueix en millora de l'histograma generat.
Recomanació: Provar-lo. Si no funciona, no és un problema — es pot revertir fàcilment.
2. Loss espectral (FFT / wavelets)
Veredicte: ⚠️ Risc alt. La FFT és global — un shift de 1 MeV en un pic ja trenca la correlació espectral. El model ja té un bias de posició (W1(z) > 0). La FFT podria fer-ho pitjor al penalitzar desplaçaments.
Recomanació: Descartat per ara. Només si el problema és clarament de "amplitud suau" (no de shift).
3. Loss heterocedàstic (σ après) / Poisson NLL
Veredicte: ⚠️ Interessant però indirecte. El model ja aprèn variances implícites a través dels errors residuals. El σ après podria funcionar si els errors als pics són sistemàticament més grans (ho són). Però la Poisson NLL només té sentit si els targets són counts discrets — aquí tenim valors continus d'edep.
Recomanació: Poisson NLL descartat (no és counts). Heterocedàstic provaria si el pas 1 no funciona.
4. Capa de sortida parametritzada (Lorentzians)
Veredicte: 🧠 És la idea més elegant del document, però amb un problema de fons que cal entendre.
La qüestió: el model no prediu directament un histograma d'edep. Predix hits (r,z,t,edep). L'histograma es construeix a posteriori sumant tots els hits. Per tant:
No hi ha una "sortida d'histograma" a què afegir Lorentzians
La sortida són N hits amb (r,z,t,edep)
Una capa parametritzada tindria sentit si:
- 
El model tingués una sortida addicional que predigués la densitat d'edep
- 
O si l'arquitectura canviés per generar l'espectre d'edep directament
Actualment, el model genera hits discrets. Els "pics" són una conseqüència emergent de quins hits es generen, no una sortida directa.
Recomanació: Descartat per a l'arquitectura actual. Seria una reestructuració major (com Track 2).
5. Cap auxiliar predictiva (peakness)
Veredicte: ⚠️ Pot ajudar, però indirecte. Si la cap prediu "on hi ha pics" basat en el target, obliga el model a aprendre representacions de l'estructura espectral. Però això només té sentit si el model té alguna sortida intermèdia que es pugui correlacionar amb "peakness".
Actualment, el model treballa a nivell de tokens (hits individuals). No hi ha cap nivell "global" de l'espectre.
Recomanació: Només si el model evoluciona a una sortida de densitat/espectre.
6. Hard example mining temporal
Veredicte: ⚠️ Cost-benefit dubtós. En un model de generació de hits, els "hard examples" no són bins individuals sinó events complets amb certs patrons de ressonància. Si no hi ha manera d'identificar quins events tenen pics i oversample-los, no té gaire sentit.
Recomanació: Descartat per ara.
7. Atenció / U-Net
Veredicte: ⚠️ Canvi arquitectural major. Actualment l'arquitectura és EPiC-FM amb transformer layers. Passar a U-Net amb skip connections seria una refactorització massiva.
Recomanació: Descartat per ara.
Resum executiu
#	Estratègia	Cost	Guany	Veredicte
1	Loss curvatura/prominència	~1 línia	Alta	✅ Primer pas
2	FFT al loss	Poques línies	Mitjana (risc shift)	⛔ Descartat
3	Heterocedàstic / Poisson	Moderat	Baixa-Mitjana	⛔ Descartat
4	Lorentzians parametritzats	Major refactor	Alta (teòric)	⛔ Requereix nova arquitectura
5	Cap peakness	Moderat	Baixa	⛔ Només amb sortida de densitat
6	Hard example mining	Moderat	Baixa	⛔ No aplica a generació de hits
7	U-Net / atenció	Major refactor	Alta	⛔ Canvi arquitectural major
La veritat incòmoda
El problema no és de loss ni d'arquitectura — és de capacitat.
El model EPiC-FM actual:
1. 
Rep E_in com a escalar continu
2. 
Genera N hits amb (r,z,t,edep)
3. 
L'histograma final és una suma estocàstica
Les ressonàncies són estructures fines a l'espectre d'edep. El model no té cap mecanisme per "sabre" on posar-les:
- 
L'embedding de E_in (Linear o bins) només determina la distribució general de hits
- 
No hi ha cap "head" de ressonància ni cap terme de loss que penalitzi específicament la pèrdua de pics
- 
Amb 500k iteracions, el model aprèn la distribució marginal (W1 baixa) però no les estructures fines (RI ~0.1)
Les opcions reals són:
1. 
Pass 1 (curvatura loss) — costa res, val la pena provar
2. 
Reestructuració major — el model hauria de generar una densitat contínua d'edep, no hits discrets (això sí que seria Track 2)
3. 
Cross-section com input — la teva opció alternativa (que prefereixes evitar per ara)
El punt 2 és el que diu el document quan parla de "Capa de sortida parametritzada" — però caldria repensar tot el pipeline de generació de hits.
