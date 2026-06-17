# Steel V11.0 — Conclusiones y propuestas de mejora

**Data:** 2026-06-17  
**Modelo:** NTO V11.0 (copia de V10.3)  
**Material:** Steel (Stainless Steel 304-like)  
**Training:** 30 epochs, flat-log 0.025eV–14.1MeV, 1M events

## 1. Resultados del pipeline completo

| Paso | Estado | Detalles |
|------|--------|----------|
| reaction_catalog.py | ✅ | 49 reacciones (H, C, He + Fe, Cr, Ni, Mn, Si, P, S, N) |
| material.py | ✅ | Steel: Z_eff=25.42, A_eff=54.55, ρ=8.0 g/cm³ |
| build_dataset_v110.sh | ✅ | 36.5M steps, ~10% UNKNOWN (isótopos naturales) |
| train_nto_v110.py | ✅ | 30 epochs, val_loss=-4.25, 638s |
| compare_v110_parallel.sh | ✅ | Sampling + compare + plots |
| compare_one_step_v110.py | ✅ | Validación atómica por head |

## 2. Métricas por energía

| Energía | edep_W1 | Δd_W1 | cosθ_W1 | hits_ratio | Observación |
|---------|---------|-------|---------|------------|-------------|
| 0.025eV | 0.00001 | 0.026 | 0.006 | 1.04 | ✅ Excelente |
| 0.1eV | 0.00000 | 0.022 | 0.007 | 1.07 | ✅ Excelente |
| 1eV | 0.00000 | 0.036 | 0.008 | 1.09 | ✅ Bueno |
| 10eV | 0.00000 | 0.025 | 0.008 | 1.09 | ✅ Bueno |
| 100eV | 0.00000 | 0.027 | 0.008 | 1.13 | ✅ Bueno |
| 1keV | 0.00000 | 0.015 | 0.007 | 1.18 | ✅ Bueno |
| 10keV | 0.00002 | 0.005 | 0.008 | 1.28 | ⚠️ Acceptable |
| 100keV | 0.001 | 0.694 | 0.042 | 5.33 | ❌ Malo (overfitting) |
| 1MeV | 0.014 | 1.967 | 0.225 | 13.88 | ❌ Malo (overfitting) |
| 6MeV | 0.009 | 0.122 | 0.018 | 1.05 | ⚠️ Acceptable |
| 14MeV | 0.005 | 0.129 | 0.009 | 0.90 | ✅ Bueno |

## 3. Análisis de la distribución de hits

La distribución de steps en el training data **no es plana** por década:

| Rango energético | % steps | Física |
|------------------|---------|--------|
| 0.01–0.1 eV | 1.2% | Captura rápida, pocos steps |
| 0.1–1 eV | 5.9% | Thermalización |
| **1–10 eV** | **17.8%** | Muchas interacciones (resonancias) |
| **10–100 eV** | **26.9%** | Máximo de interacciones |
| **100–1000 eV** | **25.0%** | Muchas interacciones |
| 1–10 keV | 13.7% | Menos interacciones |
| 10–100 keV | 5.8% | |
| 100keV–1MeV | 2.9% | |
| **1–10 MeV** | **0.9%** | Pocos steps por evento |
| 10–14 MeV | 0.1% | |

**Consecuencia:** El modelo ve ~70% de los steps a 1–100 eV, por eso rinde bien allí. A >1 MeV tiene muy pocos ejemplos de training.

## 4. Reacciones detectadas vs UNKNOWN

| Categoría | Reacciones | % steps |
|-----------|------------|---------|
| En catálogo (49) | Fe, Cr, Ni, C, Mn, Si, P, S, N + H, He | ~90% |
| UNKNOWN (isótopos) | ⁵³Cr, ⁵⁴Fe, ⁵⁰Cr, ⁶⁰Ni, ⁶²Ni, etc. | ~10% |

Los UNKNOWN son isótopos naturales que Geant4 simula pero no están en el catáleg. Las principales:
- ⁵³Cr (24053): 1.35M hits (3.7%)
- ⁵⁴Fe (26054): 864K hits (2.4%)
- ⁵⁰Cr (24050): 449K hits (1.2%)
- ⁶⁰Ni (28060): 384K hits (1.0%)

## 5. Propuestas de mejora

### 5.1. Weighted loss por bin de energía (PRIORITARIO)

**Problema:** El modelo concentra el loss en 1–100 eV por tener más steps.

**Solución:** Aplicar un peso w(E) inversamente proporcional al log-count de steps por bin:

```python
# En train_nto_v110.py, después de cargar el dataset:
bin_counts = np.histogram(log_E_pre, bins=edges)[0]
weights = 1.0 / (bin_counts + 1)
weights = weights / weights.mean()  # Normalizar
```

**Impacto estimado:** Mejora significativa a >100 keV sin degradar 1–100 eV.

### 5.2. Sampling estratificado durante training

**Problema:** DataLoader samplea uniformemente del tensor, que tiene más rows a 1–100 eV.

**Solución:** Mantener un índice por bin energético y samplear proporcionalmente:

```python
# Crear bins y samplear igual por bin
energy_bins = torch.bucketize(log_E_pre, edges)
for batch in range(n_batches):
    # Samplear N/bins_per_batch de cada bin
    ...
```

### 5.3. Más eventos a energías extremas

**Problema:** 1M events flat-log produce ~1% de steps a >1 MeV.

**Solución:** Generar eventos extra a energías específicas:
- 10M events a 1–14 MeV (para tener ~10% de steps en ese rango)
- Combinar con los 1M flat-log existentes

**Acción:** Modificar `train_Steel_flatlog_gps_1000000.mac` para generar más events a high-energy, o crear macros adicionales.

### 5.4. Añadir isótopos al catáleg (OPCIONAL)

**Problema:** ~10% de steps son UNKNOWN (isótopos naturales).

**Solución:** Añadir los isótopos más frecuentes al `reaction_catalog.py`:
- ⁵³Cr (24053): elastic + capture
- ⁵⁴Fe (26054): elastic + capture  
- ⁵⁰Cr (24050): elastic
- ⁶⁰Ni (28060): elastic + capture

**Impacto:** Reducir UNKNOWN de ~10% a ~3%. N_REACTIONS pasaría de 49 a ~60.

### 5.5. Regularización para evitar overfitting

**Problema:** Overfitting evidente a 1 MeV (hits_ratio=13.88).

**Opciones:**
1. Aumentar weight_decay (1e-5 → 1e-4)
2. Añadir dropout al encoder (0.1)
3. Early stopping más agresivo
4. Data augmentation (perturbaciones suaves a las variables)

## 6. Priorización

| Mejora | Impacto | Esfuerzo | Prioridad |
|--------|---------|----------|-----------|
| Weighted loss | Alto | Bajo | 🔴 ALTA |
| Sampling estratificado | Alto | Medio | 🟡 MEDIA |
| Más eventos high-energy | Medio | Alto (re-simular) | 🟡 MEDIA |
| Añadir isótopos | Bajo | Bajo | 🟢 BAJA |
| Regularización | Medio | Bajo | 🟢 BAJA |

## 7. Conclusiones

1. **El sistema funciona de principio a fin** con Steel como nuevo material.
2. **La distribución de hits no es óptima** para entrenar — el modelo ve 70% de los steps a 1–100 eV.
3. **La mejora más impactante y sencilla** es implementar weighted loss por bin energético.
4. **Los UNKNOWN (10%) son aceptables** para una primera prueba — se pueden añadir después.
5. **Para producción**, combinar weighted loss + más eventos high-energy daría el mejor resultado.

---

*Documento generado automáticamente por el pipeline V11.0*
