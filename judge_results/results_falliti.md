# Risultati — Falliti (62 GUID)

**Data:** 2026-05-13 | **Metodo:** Analisi JSON (nessuna visione immagini)  
**Popolazione:** 62 GUID che non hanno prodotto output valido (0 item con promo match)

---

## Sommario Classificazioni

| Classificazione | Conteggio | % |
|-----------------|-----------|---|
| `input_problem` | **29** | 46.8% |
| `unclear` | **33** | 53.2% |
| `pipeline_issue` | 0 | 0.0% |

---

## Dettaglio per Sottocategoria

### 1. zero_item (25 GUID) → `input_problem`

| Caratteristica | Valore |
|----------------|--------|
| Item estratti | 0 |
| Flag preprocessing | Nessuno (CLEAN) |
| Classificazione | **input_problem** |

**Analisi:** Il pipeline non ha estratto alcun item. In assenza di flag di errore, il problema più probabile è la qualità dell'immagine (ricevuta illeggibile, foto sfocata, orientamento estremo) che ha impedito l'estrazione ma non è stata rilevata come errore esplicito. Classificati come `input_problem` perché il pipeline non ha evidenze di failure proprio ma l'input non era processabile.

**Statistiche:**
- Items medi: 0.0
- Immagini per GUID: tipicamente 1–2

---

### 2. scontrino_troncato (19 GUID) → `unclear`

| Caratteristica | Valore |
|----------------|--------|
| Flag preprocessing | TRUNCATED e/o AUTO_REPAIRED |
| Item estratti | 1–143 (media ≈ 26) |
| Classificazione | **unclear** |

**Analisi:** Questi GUID hanno il flag `TRUNCATED` o `AUTO_REPAIRED` — il pipeline ha rilevato che lo scontrino era parziale/troncato. Nonostante ciò, ha estratto item (range 1–143). Questo suggerisce che il pipeline abbia **gestito parzialmente il problema** (AUTO_REPAIRED), producendo output anche con input incompleto.

La classificazione `unclear` riflette l'ambiguità: non è chiaro se il "fallimento" sia da attribuire all'input (scontrino fisicamente troncato) o al pipeline che non ha estratto tutte le promozioni possibili dall'immagine disponibile.

**Statistiche:**
- Items medi: ~26 per GUID
- Range: 1 item (quasi vuoto) → 143 item (scontrino lungo)
- GUID con AUTO_REPAIRED: il pipeline ha tentato la riparazione automatica

---

### 3. non_receipt_retry (14 GUID) → `unclear`

| Caratteristica | Valore |
|----------------|--------|
| Flag preprocessing | NOT_A_RECEIPT_RETRY |
| Item estratti | 5–38 (media ≈ 17) |
| Classificazione | **unclear** |

**Analisi:** Il flag `NOT_A_RECEIPT_RETRY` indica che in una prima analisi il pipeline ha classificato l'immagine come "non scontrino" e ha effettuato un retry. Il fatto che tutti i 14 GUID abbiano estratto item (5–38) nella versione finale suggerisce che **il pipeline abbia recuperato correttamente** al secondo tentativo.

Questi GUID sono stati probabilmente misfotografati (documento storto, parte di scontrino, ricevuta di un bar) ma il retry ha permesso l'estrazione. Non classificati come `pipeline_issue` perché l'output esiste; non classificati come `input_problem` perché il problema potrebbe essere solo apparente (il retry ha funzionato).

**Statistiche:**
- Items medi: ~17 per GUID
- Range: 5–38 item

---

### 4. contenuto_scarso (2 GUID) → `input_problem`

| Caratteristica | Valore |
|----------------|--------|
| Flag preprocessing | SKIPPED_LOW_CONTENT |
| Item estratti | 0 |
| Classificazione | **input_problem** |

**Analisi:** Flag esplicito `SKIPPED_LOW_CONTENT` — il pipeline ha rilevato contenuto testuale insufficiente prima ancora di tentare l'analisi. Il problema è definitivamente nell'input: immagine con troppo poco testo leggibile (ricevuta mini, parte di scontrino, bassa risoluzione).

---

### 5. immagine_illeggibile (2 GUID) → `input_problem`

| Caratteristica | Valore |
|----------------|--------|
| Flag preprocessing | REJECTED |
| Item estratti | 0 |
| Classificazione | **input_problem** |

**Analisi:** Flag esplicito `REJECTED` — l'immagine è stata scartata a monte. Causa tipica: immagine completamente illeggibile (foto sfocatissima, buio totale, immagine corrotta). Problema definitivamente nell'input.

---

## Distribuzione Completa

```
Totale Falliti: 62

input_problem (29):
  ├── zero_item:          25  (senza flag errore, 0 item — input non processabile)
  ├── contenuto_scarso:    2  (SKIPPED_LOW_CONTENT)
  └── immagine_illeggibile: 2  (REJECTED)

unclear (33):
  ├── non_receipt_retry:  14  (NOT_A_RECEIPT_RETRY → ma item estratti, pipeline recovered)
  └── scontrino_troncato: 19  (TRUNCATED/AUTO_REPAIRED → item parziali estratti)

pipeline_issue (0):
  └── (nessun caso identificato)
```

---

## Osservazioni

1. **Nessun pipeline_issue identificato.** Nei 62 GUID analizzati, in nessun caso il pipeline ha evidentemente fallito su un input valido. I flag di preprocessing indicano sempre un problema rilevato (REJECTED, SKIPPED_LOW_CONTENT, TRUNCATED, NOT_A_RECEIPT_RETRY).

2. **Il 53.2% è "unclear"** perché il pipeline ha comunque prodotto output (item estratti) nonostante i flag negativi. Questo è un comportamento positivo: il pipeline preferisce estrarre qualcosa anche con input degradato.

3. **I zero_item (40.3% dei falliti) sono il caso peggiore** — nessun output, nessun flag esplicito. Potrebbero beneficiare di un reject filter più sensibile nella fase di preprocessing.

4. **Tasso di fallimento nell'intera popolazione: 6.2%** (62/1000). Considerando che ~33 di questi hanno comunque estratto item parziali, il tasso di "fallimento completo" è più vicino al 2.9% (29/1000).
