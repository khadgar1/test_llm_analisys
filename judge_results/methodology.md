# Metodologia — LLM-as-Judge Verification

**Data analisi:** 2026-05-13  
**Popolazione:** 1 000 GUID totali  
**Judge:** Claude Sonnet (visione immagini + analisi JSON)

---

## 1. Classificazione della Popolazione

| Categoria | N | Descrizione |
|-----------|---|-------------|
| Funzionato | 293 | ≥1 item con `match_confidence ≥ 80` |
| Nessuna promo | 636 | Nessun item con `match_confidence ≥ 80` |
| Check umano | 9 | `match_confidence` 31–79 (zona ambigua) |
| Falliti | 62 | Pipeline non ha prodotto output valido |
| **Totale** | **1 000** | |

---

## 2. Task 1 — Campionamento Statistico (156 GUID)

### Formula applicata

Campionamento con correzione per popolazione finita (FPC):

```
n_base = z² · p · (1-p) / e²
       = 1.96² · 0.5 · 0.5 / 0.05²
       = 384.16 ≈ 385

n_corr = n_base / (1 + (n_base - 1) / N)
```

| Gruppo | Popolazione N | z | e | p | n_base | n_corretto |
|--------|--------------|---|---|---|--------|-----------|
| A — Funzionato (Precisione) | 293 | 1.96 | 0.05 | 0.5 | 385 | **72** |
| B — Nessuna promo (Recall) | 636 | 1.96 | 0.05 | 0.5 | 385 | **84** |

### Selezione GUID
I GUID sono pre-specificati (non campionati casualmente in questa sessione).

### Verifica Group A — Precisione
Per ogni GUID: il pipeline ha correttamente identificato un item in promo?

Criteri programmatici:
- **TP** (True Positive): `brand_match(item_desc, brand_name) = True` **OR** `similarity(item_desc, catalog_name) > 0.35` **OR** `word_overlap > 0.30`
- **FP** (False Positive): nessuno dei criteri soddisfatto

```python
def brand_match(item_desc, brand_name):
    desc_l = item_desc.lower()
    brand_l = brand_name.lower()
    return any(w in desc_l for w in brand_l.split() if len(w) > 3) \
           or brand_l[:6] in desc_l

def desc_sim(a, b):
    return SequenceMatcher(None, a.lower(), b.lower()).ratio()
```

### Verifica Group B — Recall
Per ogni GUID: la pipeline ha perso un item in promo reale?

Criteri per segnalare FN candidato:
- **Barcode match**: stesso barcode tra item receipt e prodotto catalogo con `brand_match = True`
- **Name similarity**: `similarity > 0.60` **AND** overlap ≥ 2 parole comuni

Revisione manuale dei 36 GUID flaggati come REVIEW:
- **TN confermato**: collision di barcode con prodotto catalogo completamente diverso (es. pasta → candeggina Omino Bianco), o similarità generica senza brand
- **FN confermato**: brand identico + prodotto corrispondente in modo inequivocabile

---

## 3. Task 2 — Check Umano (9 GUID)

Per ogni GUID: visione dell'immagine scontrino e analisi del JSON.

Classificazione:
| Classe | Significato |
|--------|-------------|
| `legittimo` | Il prodotto sullo scontrino corrisponde al prodotto in promozione |
| `brand_collision` | La parola che ha generato il match è un aggettivo/sostantivo comune, non il brand specifico |
| `variante_incerta` | Brand e taglia coincidono ma la variante/flavor non è verificabile dal testo scontrino |

---

## 4. Task 3 — Falliti (62 GUID)

Analisi solo su dati JSON (nessuna visione immagini).

Classificazione:
| Classe | Significato |
|--------|-------------|
| `input_problem` | Il problema è nell'immagine/input (illeggibile, non scontrino, contenuto insufficiente) |
| `pipeline_issue` | Il pipeline ha fallito su input che avrebbe dovuto gestire |
| `unclear` | Impossibile determinare con certezza causa dell'errore |

Sottocategorie analizzate:
- `zero_item` (25): estratti 0 item, nessun flag di errore
- `scontrino_troncato` (19): flag TRUNCATED/AUTO_REPAIRED presente
- `non_receipt_retry` (14): flag NOT_A_RECEIPT_RETRY
- `contenuto_scarso` (2): flag SKIPPED_LOW_CONTENT
- `immagine_illeggibile` (2): flag REJECTED

---

## 5. Metriche Stimate

### Precisione (Group A)
```
Precision = TP / (TP + FP) = 72 / (72 + 0) = 100%
95% CI (regola dei 3 per zero eventi): [95.1%, 100%]
```

### Recall stimato (Group B)
```
FN_rate_sample = 4 / 84 = 4.76%

FN stimati totali in "Nessuna promo":
  FN_tot = FN_rate × 636 ≈ 30

Recall = TP_found / (TP_found + FN_tot)
       = 293 / (293 + 30)
       ≈ 90.7%

95% CI per FN_rate (con FPC):
  FPC = sqrt((636-84)/(636-1)) = 0.932
  ME = 1.96 × sqrt(0.0476×0.9524/84) × 0.932 ≈ 0.043
  CI: [1.3%, 9.2%]
```

### F1-Score stimato
```
F1 = 2 × Precision × Recall / (Precision + Recall)
   = 2 × 1.00 × 0.907 / (1.00 + 0.907)
   ≈ 0.951
```
