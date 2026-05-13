# Metodologia — Campione Statistico

## Perché non verificare tutti i 965 GUID

Verificare manualmente 965 GUID (funzionato + nessuna promo) richiederebbe tempo e risorse
sproporzionati rispetto al valore informativo aggiuntivo. La teoria statistica del campionamento
permette di stimare la qualità dell'intera popolazione da un sottoinsieme rappresentativo.

> **Nota sulla classificazione:** La pipeline ha prodotto 306 GUID «Funzionato» (≥1 promo item
> con promo_match.match_confidence ≥ 80) e 659 GUID «Nessuna promo» (nessun is_promo_item=true,
> items > 0), per una popolazione combinata di 965 GUID. I 25 GUID con 0 item estratti sono
> classificati come «Fallito» e non fanno parte del campione.

## Formula del campione

Per una stima con:
- Confidenza: 95% (z = 1.96)
- Margine di errore: ±10%
- Proporzione attesa conservativa: p = 0.5 (massimizza la dimensione del campione)

```
n = (z² × p × (1-p)) / e²
n = (1.96² × 0.5 × 0.5) / 0.10²
n = (3.8416 × 0.25) / 0.01 = 96
```

Con correzione per popolazione finita (N = 965):

```
n_corretto = n / (1 + (n-1)/N)
n_corretto = 96 / (1 + 95/965)
n_corretto = 96 / 1.0984 ≈ 87.4 ≈ 88
```

**Campione minimo: 88 GUID**

Utilizziamo **90 GUID (45 per gruppo)** per semplicità operativa, mantenendo
lo stesso margine di errore di ±10% con confidenza 95%.

## Strategia di stratificazione

Il campione non è casuale puro ma stratificato per garantire rappresentatività e
coprire le zone di rischio più elevato.

### Gruppo A — Funzionato (45 GUID da 306)

Obiettivo: verificare la **PRECISIONE**.

- La pipeline dice che ci sono promo confermati con confidence ≥ 80.
- Verifichiamo: quei prodotti sono davvero nel catalogo promo E corrispondono
  all'item sullo scontrino?
- Domanda: quanti sono veri positivi vs falsi positivi?

Distribuzione per confidence band:

| Strato | Range | Popolazione | Campione | Motivazione |
|--------|-------|-------------|---------|-------------|
| Bassa | 80–89 | 142 | 20 | Zona critica: rischio FP più alto |
| Media | 90–95 | 61  | 15 | Zona intermedia |
| Alta  | 96–100| 103 | 10 | Alta confidenza: attesi pochi FP |

Selezione: `random.sample` con `seed=42` per riproducibilità.

### Gruppo B — Nessuna promo (45 GUID da 659)

Obiettivo: verificare il **RECALL**.

- La pipeline dice che non ci sono prodotti promo in questo scontrino.
- Verifichiamo: guardando le immagini e il catalogo, c'era davvero nessun
  prodotto promo nel carrello?
- Domanda: quanti sono veri negativi vs falsi negativi (promo mancati)?

Distribuzione per numero di item:

| Strato | Range | Popolazione | Campione | Motivazione |
|--------|-------|-------------|---------|-------------|
| Corto | 1–8 item   | 305 | 15 | Scontrini brevi — meno prodotti da controllare |
| Medio | 9–15 item  | 163 | 15 | Range centrale |
| Lungo | >15 item   | 191 | 15 | Più articoli → più probabilità di promo mancato |

## Metodo di verifica

### Gruppo A — Verifica precisione (item-level)

Per ogni item con `is_promo_item=true` e `promo_match.match_confidence ≥ 80`:

1. Il `promo_match.product_guid` esiste nel catalogo attuale?
2. Il `promo_match.brand_name` corrisponde alla descrizione sull'item?
3. La descrizione item è compatibile con il nome prodotto in catalogo?
4. Verifica visiva su almeno 1 immagine per GUID (spot-check su tutti 45).

Criteri verdetto item:
- **TP**: brand + product type corrispondono (incluso beneficio del dubbio per catalog rotation)
- **FP_brand**: stesso brand, SKU/prodotto diverso (es. 15-pack vs 10-pack, o diversa referenza)
- **FP_category**: categoria simile, brand sbagliato
- **FP_other**: corrispondenza non giustificabile

Verdetto GUID: **pass** se tutti gli item promo sono TP.

### Gruppo B — Verifica recall (GUID-level)

1. Analisi programmatica: barcode degli item contro catalogo (exact match); 
   similarity testuale nome item vs nome prodotto catalogo (soglia 0.55).
2. Reclassificazione manuale di ogni potenziale FN: esclusione collisioni
   barcode (data quality), esclusione private label, esclusione generic names.
3. Verifica visiva su tutti i GUID con potenziali FN, più spot-check sui TN.

Criteri verdetto GUID:
- **TN**: nessun prodotto promo evidente trovato
- **FN_obvious**: prodotto promo chiaramente identificabile e non matchato dalla pipeline
- **FN_borderline**: possibile match ma incerto (prodotto generico, brand non confermato)

Verdetto GUID: **pass** se TN o FN_borderline.

## Note metodologiche

- **Catalog rotation**: il catalogo promo ruota. Un barcode presente ma mappato a
  prodotto diverso indica probabile problema di data quality nel catalogo, non un
  errore della pipeline. Questi casi sono classificati come TN con nota.
- **Private label**: un item con descrizione generica (es. "Frutti di Bosco") senza
  brand visibile sull'item non è classificabile come FN solo per similarità testuale.
- **Beneficio del dubbio**: in assenza di prova contraria, si dà il beneficio del
  dubbio alla pipeline (TN/TP) per i casi borderline.
- **Collisioni barcode**: 8+ casi nel Gruppo B avevano barcode nel catalogo che
  mappavano a prodotti completamente diversi (es. pasta → detersivo). Questi sono
  data quality issues del catalogo, non miss della pipeline.
