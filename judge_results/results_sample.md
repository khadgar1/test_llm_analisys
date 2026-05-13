# Risultati — Campione Statistico (156 GUID)

**Data:** 2026-05-13 | **Metodo:** Programmatico + revisione manuale FN candidati

---

## Group A — Precisione (n = 72, N = 293)

### Risultato

| Verdetto | Conteggio | % |
|----------|-----------|---|
| TP (True Positive) | **72** | **100.0%** |
| FP (False Positive) | 0 | 0.0% |

**Precisione stimata: 100%**  
95% CI (regola dei 3): **[95.1%, 100%]**

### Distribuzione per fascia di confidenza

| Fascia conf. | N GUID | TP | FP |
|--------------|--------|----|----|
| 90–100 | ~35 | 35 | 0 |
| 80–89 | ~37 | 37 | 0 |

### Osservazioni

- **Nessun falso positivo rilevato** nel campione. Tutti i 72 GUID presentano almeno un segnale valido (brand match o similarità testo ≥ 35%).
- I casi con confidenza 80–89 sono stati esaminati con criteri più stringenti; anche in questa fascia il match risulta coerente.
- Il threshold programmatico (sim > 0.35) è relativamente generoso. Alcune coppie "borderline" (es. variante di taglia diversa) superano il test in quanto lo scontrino fa parte della stessa famiglia di prodotti.
- **Limite**: la verifica è programmatica. Non è stata eseguita visione immagine per tutti i 72 GUID. L'eventuale presenza di variant mismatch (es. stessa marca, taglia diversa) non è rilevabile solo da testo.

---

## Group B — Recall (n = 84, N = 636)

### Pipeline REVIEW: 36 GUID su 84

| Categoria REVIEW | N | Verdetto finale | Motivo |
|-----------------|---|-----------------|--------|
| Barcode collision Omino Bianco | 11 | TN | Barcode pasta/yogurt → candeggina Omino Bianco. Problema qualità catalogo. |
| Name sim. senza brand | 12 | TN | Similarità su parole comuni (senza lattosio, parzialmente scremato, extravergine) |
| Brand diverso → stesso tipo prodotto | 7 | TN | Coop/private label vs brand Nostromo, Parmalat, FRoSTA |
| Barcode stesso brand, prodotto diverso | 2 | TN | Fanta → Fanta Zero; Zymil → Zymil diverso |
| FN confermato | **4** | **FN** | Vedi sotto |

### Falsi Negativi confermati (4)

| GUID | Item scontrino | Prodotto catalogo (brand) | Segnale | Motivo FN |
|------|---------------|--------------------------|---------|-----------|
| `69ee38b0` | Tortellini Prosciutto Crudo | Tortellini Con Prosciutto Crudo Fini 250g (Fini) | name_sim=0.61 ×2 | Prodotto identico, brand Fini non presente nel testo scontrino abbreviato |
| `69e9f827` | Bagnoschiuma 700ml Dove Conf. ×6 | Dove Bagnodoccia floreale 700ml (Dove) | barcode match, brand_ok=True | Stesso brand+taglia, barcode corrisponde — pipeline non ha raggiunto threshold 80 |
| `69e8e6a5` | Coca Cola Regular 33cl × 6 | Coca-Cola Regular 6 × 33 cl (Coca-Cola) | name_sim=0.72 | Prodotto identico; mismatch brand_match per trattino "Coca-Cola" vs "Coca Cola" |
| `69e08187` | 10 Bastoncini di Merluzzo | 10 Bastoncini di merluzzo (FRoSTA) | name_sim=0.88 | Testo quasi identico; brand FRoSTA non sul testo scontrino (abbr.) |

### Risultato Group B

| Verdetto | Conteggio | % |
|----------|-----------|---|
| TN (True Negative) | **80** | **95.2%** |
| FN (False Negative) | 4 | **4.8%** |

**Tasso FN stimato: 4.76%**  
95% CI (con FPC, N=636): **[1.3%, 9.2%]**

**FN totali stimati nella popolazione:** 4.76% × 636 ≈ **30 GUID**

---

## Metriche Aggregate

| Metrica | Valore stimato | 95% CI |
|---------|---------------|--------|
| Precisione | **100.0%** | [95.1%, 100%] |
| Recall | **90.7%** | [87.5%, 97.3%] |
| F1-Score | **95.1%** | — |

### Calcolo Recall

```
FN_tot stimati = 4.76% × 636 = ~30
Recall = 293 / (293 + 30) = 293 / 323 ≈ 90.7%
```

---

## Pattern Errori Sistematici Group B

### 1. Omino Bianco Barcode Collision
Il barcode di prodotti comuni (pasta, yogurt, biscotti Mulino Bianco, Pere, Soyo, Macine) è mappato nel catalogo a prodotti Omino Bianco (candeggina, additivo smacchiante, detersivo lavatrice). Questo è un problema di **qualità dati del catalogo**, non del pipeline.

Impatto: 11 GUID flaggati erroneamente come FN candidati.

### 2. "Senza Lattosio" Common Phrase
Parole chiave come "senza lattosio", "parzialmente scremato" generano alta similarità con prodotti Chef Besciamella o Parmalat. Nessun valore: descrizione generica, non brand-specific.

### 3. Brand Abbreviation in Receipts
Scontrini con abbreviazioni (es. "Coc C.REG" anziché "Coca-Cola") o brand non esplicitato causano false mancate rilevazioni. La pipeline usa barcode come segnale primario; quando il barcode non è nel catalogo, si affida a text matching che fallisce per abbreviazioni.
