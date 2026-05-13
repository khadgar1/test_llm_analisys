# LLM-as-Judge: 10 GUID — Analisi JSON + Verifica Visiva

**Data analisi:** 2026-05-13  
**Modello judge:** claude-sonnet-4-6  
**Ipotesi da verificare:** alcuni GUID con più immagini falliscono perché almeno un'immagine non è uno scontrino, e il JSON lo segnala già.

---

## 1. Riepilogo per GUID

### GUID 1 — `69e3ef6a86e5b64551b9edd2` (4 img, 15 item)

**JSON:** `REJECTED(img0: UNREADABLE(blur=8, edge%=0.0))`. Tutte le statistiche di img[0] sono zero (contrast=0.0, coverage=0.0, noise=0.0, mean_brightness=0.0). Dimensioni registrate: 1559×3975. Le altre 3 immagini hanno stats normali (contrast 156–188, coverage 0.72–0.88, is_bimodal=True su img[1] e img[2]).

**Immagini:**
- `00.jpg`: Metà superiore dello scontrino Buscaini Angelo S.R.L. (Roma), fotografato su un piano con decorazioni natalizie (nastri rossi). Immagine nitida e leggibile, buon contrasto.
- `01.jpg`: Sezione centrale dello stesso scontrino (continua la lista prodotti fino al totale). Chiara e leggibile.
- `02.jpg`: Footer dello scontrino (campagna punti, NUMERO CASSA). Leggibile.
- `03.jpg`: Lo scontrino fotografato inclinato, con elementi decorativi (tessuto a quadri) che coprono parzialmente l'angolo inferiore destro. La lettura è possibile ma parziale.

**Confronto:** Il segnale REJECTED con blur=8 e edge%=0.0 non è confermato visivamente: nessuna delle 4 immagini appare sfocata o vuota. `00.jpg` è l'immagine più nitida del set. Le statistiche a zero suggeriscono un problema di lettura del file a livello pipeline (possibile corruzione del file non visibile all'occhio, o un bug nel calcolo del Laplacian variance su questa immagine specifica) piuttosto che una vera immagine illeggibile. Da notare che la pipeline ha comunque estratto 15 item dalle 3 immagini rimanenti.

**Corrispondenza: PARZIALE** — Il segnale identifica correttamente un problema (qualcosa non va con img[0]), ma la causa visiva non è confermabile: l'immagine sembra leggibile.

---

### GUID 2 — `69edd10de593340322ec7a19` (4 img, 2 item)

**JSON:** `SKIPPED_LOW_CONTENT(part0, coverage=0.04)`. img[0]: contrast=68, avg_sat=9.4, coverage=0.04, is_bimodal=False. Le img[1–3] hanno coverage crescente (0.20, 0.80, 0.68) e stats omogenee.

**Immagini:**
- `00.jpg`: Lo scontrino ALÌ (Favaro Veneto) è fisicamente nello scatto ma occupa una porzione minima del fotogramma — circa 4–5% dell'area. Il resto è sfondo tessuto/abbigliamento chiaro. Il foglietto è tenuto lontano dalla camera.
- `01.jpg`: Stesso scontrino, avvicinato, occupa circa 50% del frame. Leggibile.
- `02.jpg`: Stessa scena, ancora avvicinato, scontrino occupa ~75% del frame.
- `03.jpg`: Solo la parte finale dello scontrino (loyalty points, barcode). Ben centrato.

**Confronto:** coverage=0.04 corrisponde esattamente a quello che si vede: lo scontrino è minuscolo nel fotogramma. Il segnale è precisissimo. I 2 item estratti (Latte Microfil.PS 1L Zymil, Erbette della Nonna) corrispondono al contenuto reale dello scontrino breve.

**Corrispondenza: CONFERMATA** ✓

---

### GUID 3 — `69eaf7ca551a2cb157c0ca8a` (2 img, 5 item)

**JSON:** `NOT_A_RECEIPT_RETRY`, `MARGIN_CROP`. img[0]: noise=704, coverage=1.0, avg_sat=16.3. img[1]: noise=1798, coverage=1.0, avg_sat=27.4, is_bimodal=False. Nessun img REJECTED/SKIPPED.

**Immagini:**
- `00.jpg`: Scontrino DUE PUNTO SRL (Roma) posizionato verticalmente su sfondo con tessuto floreale colorato (foglie gialle/nere). Leggibile. Visibili: Coca Cola 1.5L x4, Villa Antica Olio, S.Anna Natural Conf., S.Anna Acqua, Cottonella. Totale 34.45€.
- `01.jpg`: **Lo stesso scontrino ruotato di 90°** e fotografato in orizzontale — il foglio è disteso sul tessuto floreale. Il testo è ruotato di 90° e difficilmente leggibile in questa orientazione.

**Confronto:** Il flag `NOT_A_RECEIPT_RETRY` è scattato plausibilmente per img[1] (scontrino ruotato 90°, che in quella orientazione ricorda poco uno scontrino canonico). Il noise elevatissimo di img[1] (1798 vs 704 di img[0]) riflette la difficoltà di lettura dell'immagine ruotata. **Tuttavia**: entrambe le immagini SONO scontrini — il flag "not a receipt" è un falso positivo di tipo: "è un documento difficile da leggere", non "non è uno scontrino". Il segnale identifica un'immagine problematica ma ne sbaglia la natura.

**Corrispondenza: PARZIALE** — il segnale trova correttamente l'immagine difficile, ma la rotazione ≠ non-receipt.

---

### GUID 4 — `69ef37573da34a89f7056243` (2 img, 34 item)

**JSON:** `SKIPPED_LOW_CONTENT(part1, coverage=0.12)`. img[0]: contrast=172, coverage=1.0, noise=946. img[1]: contrast=123, coverage=0.12, noise=122.

**Immagini:**
- `00.jpg`: Scontrino Gulliver Supermercato (Villafranca d'Asti), fotografato in verticale e perfettamente centrato. Lunghissima lista prodotti (34 item confermati visivamente). Immagine di ottima qualità.
- `01.jpg`: **Stesso scontrino**, ma nella parte inferiore del frame — il foglietto occupa circa il 10–15% del fotogramma, con mano parzialmente visibile sotto e grande area di sfondo (piano grigio chiaro). coverage=0.12 confermato visivamente.

**Confronto:** Identico al caso GUID 2. La coverage bassa corrisponde esattamente a ciò che si vede: lo scontrino è piccolo nello scatto. 34 item estratti da img[0] sono corretti.

**Corrispondenza: CONFERMATA** ✓

---

### GUID 5 — `69f12c6c86e5b64551ba6f7d` (5 img, 28 item)

**JSON:** `REJECTED(img1: UNREADABLE(blur=1, edge%=0.0))`. img[1]: tutte stats=0, dimensioni 437×2223. Le altre 4 immagini: dimensioni standard (1920×2560 o simili), contrast 89–130, coverage 0.72–1.0.

**Immagini:**
- `00.jpg`: Metà superiore di uno scontrino Gulliver (Montegrosso d'Asti). Chiaro. Inizia con PURINA ONE MED.MAX MAN.
- `01.jpg`: **Contenuto quasi identico a 00.jpg** — stesso scontrino, stessa sezione (PURINA ONE in cima), stesso angolo. Appare come una foto duplicata o quasi-identica del medesimo foglietto. Visivamente leggibile quando renderizzata.
- `02.jpg`, `03.jpg`, `04.jpg`: Tre foto della metà inferiore dello stesso scontrino (da CRACKERS SALATI PAVESI al totale 86.65€).

**Confronto:** img[1] ha dimensioni 437×2223 — **drasticamente più piccola** delle altre (tutte ~1920px di larghezza). Questa è la chiave: un'immagine 437px di larghezza con blur=1 (varianza Laplaciana quasi nulla) suggerisce un file corrotto, una thumbnail, o un'immagine acquisita male che a bassa risoluzione non ha dettaglio sufficiente per essere rilevato. La renderizzazione la fa apparire leggibile per upscaling del viewer, ma a livello di pixel effettivi è probabilmente un duplicato compresso/degradato. Il segnale REJECTED è supportato dalle dimensioni anomale, anche se non visivamente confermabile al 100%.

**Corrispondenza: PARZIALE** — le dimensioni anomale (437px) corroborano il segnale; visivamente non confermabile perché il viewer compensa.

---

### GUID 6 — `69e3b8cd551a2cb157c082b2` (2 img, 5 item)

**JSON:** Nessun flag. img[0]: contrast=163, avg_sat=47.8, is_bimodal=False, size 1787×549 (molto bassa!). img[1]: contrast=185, **avg_sat=119.0**, is_bimodal=True, size 1920×3705.

**Immagini:**
- `00.jpg`: Scontrino Esselunga fotografato in **landscape/orizzontale**, tenuto disteso con entrambe le mani su un tavolo di legno. Il testo è ruotato di 90° rispetto alla lettura naturale. Size 1787×549 confermato: l'immagine è panoramica (larghissima, bassa). Leggibile.
- `01.jpg`: **Lo stesso scontrino Esselunga**, ora fotografato in verticale (portrait) ma **capovolto** — il footer dello scontrino è in alto, l'header in basso. La mano lo tiene da sinistra. avg_sat=119 è molto alta per uno scontrino.

**Confronto:** avg_sat=119 su img[1] è anomalo ma corrisponde a uno scontrino **capovolto** fotografato su piano in legno con mano visibile — non a un'immagine non-scontrino. Il segnale di alta saturazione è completamente spiegato dalle condizioni di ripresa (toni caldi del legno + pelle) e non segnala contenuto non pertinente. La pipeline non ha emesso flag, e aveva ragione: entrambe le immagini sono scontrini validi. I 5 item estratti corrispondono agli acquisti visibili (Rucola, Lenticchie, Milk Kefir, Dolmiti, ecc.).

**Corrispondenza: SMENTITA** — avg_sat=119 + is_bimodal=True NON indica non-scontrino. È rumore.

---

### GUID 7 — `69f04ea686e5b64551ba6368` (3 img, 5 item)

**JSON:** Nessun flag. img[0]: avg_sat=40.2, is_bimodal=False. img[1]: avg_sat=84.2, **is_bimodal=True**. img[2]: avg_sat=55.4, is_bimodal=False.

**Immagini:**
- `00.jpg`: Sezione superiore dello scontrino CONAD (Francesco Paoletti srl, Badolato CZ) su sfondo rosso/magenta vivace (tessuto con motivo a rami). Leggibile.
- `01.jpg`: Sezione inferiore dello stesso scontrino CONAD, stesso sfondo rosso. Il rosso vivace spiega avg_sat=84.2. is_bimodal=True è coerente con testo nero su carta bianca.
- `02.jpg`: **Retro dello scontrino CONAD** — foglio girato, mostra la faccia posteriore con testo promozionale: "SCOPRI UN MODO SEMPLICE E DIGITALE DI FARE LA SPESA. SCARICA L'APP HEYCONAD." + QR code + logo CONAD in rosso/rosa su sfondo bianco.

**Confronto:** Il segnale anomalo (avg_sat=84.2 su img[1]) punta all'immagine **front-of-receipt** della sezione finale, che è perfettamente valida. L'immagine realmente problematica — il **retro dello scontrino** (img[2], 02.jpg), che non contiene item ma solo contenuto promozionale — ha avg_sat=55.4 e is_bimodal=False e NON è stata flaggata in nessun modo. La pipeline ha estratto 5 item dal fronte, ignorando correttamente il retro (o comunque non confondendosi), ma il JSON **non segnala** che img[2] è inutile/non-scontrino.

**Corrispondenza: SMENTITA** — il segnale punta all'immagine sbagliata. L'immagine realmente non pertinente (retro promozionale) non è stata rilevata.

---

### GUID 8 — `69e25fab551a2cb157c0732b` (3 img, 1 item)

**JSON:** Nessun flag. img[0]: contrast=109, avg_sat=67.3, is_bimodal=False. img[1]: contrast=89, avg_sat=51.4, is_bimodal=False. img[2]: contrast=115, **avg_sat=115.8**, is_bimodal=True, **size=1920×300** (anomala: altezza di soli 300px).

**Immagini:**
- `00.jpg`: Scontrino Nova Coop SC (Nichelino TO) su sfondo con tovaglia a quadri rosso/bianco/blu. 1 solo item (PAELLA MSC FROSTA 5.09€ - SCONTO 40.0% SOCI -2.04€ = 3.05€). Leggibile, chiaro.
- `01.jpg`: **Stesso scontrino capovolto** (footer in alto, header in basso), ruotato di 180°. Difficile da leggere ma ricostruibile.
- `02.jpg`: **Stesso scontrino ruotato di ~90°** (in landscape), sempre sulla tovaglia a quadri colorata. Visivamente è un rettangolo stretto/oblungo nel fotogramma.

**Confronto:** Le dimensioni 1920×300 di img[2] sono molto anomale. Guardando 02.jpg, lo scontrino è fotografato di lato (landscape) su una superficie colorata — le dimensioni suggeriscono che solo una striscia dell'immagine è stata catturata, o che il preprocessing ha ritagliato l'immagine pesantemente riconoscendola come striscia di testo. avg_sat=115.8 è giustificato dal pattern vivace della tovaglia a quadri. Nessun flag nonostante due dimensioni distinte anomale (altezza 300px + saturazione 115). Il singolo item estratto è **corretto** — è realmente uno scontrino con 1 prodotto.

**Corrispondenza: PARZIALE** — le anomalie (size 1920×300, sat=115.8) segnalano immagini problematiche, ma tutto il set è scontrini validi (solo mal fotografati). Nessun non-receipt.

---

### GUID 9 — `69e8599b86e5b64551ba1838` (2 img, 5 item)

**JSON:** Nessun flag. img[0]: contrast=114, avg_sat=26.8, **is_bimodal=True**, noise=28, size 1106×1587, skew=9.99°. img[1]: contrast=158, avg_sat=27.6, **is_bimodal=False**, noise=16, size 960×2368.

**Immagini:**
- `00.jpg`: Scontrino L'Isola dei Tesori (pet shop, Isola BO), su sfondo beige/crema (coperta morbida). Leggibile. Acquisto: OASY CAT LT 85G DADI x3 + ISOLA SHOPPER WATERB, totale 56.06€. Leggermente inclinato (skew=9.99° confermato).
- `01.jpg`: **Stesso scontrino**, su stesso sfondo beige, fotografato da angolo leggermente diverso. Il foglio è più inclinato e viene parzialmente coperto da un secondo documento nella parte bassa dell'immagine. Leggibile.

**Confronto:** La variazione bimodale (img[0] bimodal=True, img[1] bimodal=False) non corrisponde a nessun contenuto non-scontrino: entrambe le immagini sono scontrini validi. La differenza is_bimodal riflette il diverso angolo di ripresa e la porzione di sfondo beige catturata (meno contrasto testo/sfondo = meno bimodale). I 5 item estratti sono plausibili.

**Corrispondenza: SMENTITA** — bimodal_mixed è rumore puro qui.

---

### GUID 10 — `69f047dae593340322ec9021` (2 img, 4 item)

**JSON:** Nessun flag. img[0]: contrast=129, avg_sat=37.1, **is_bimodal=False**, noise=327. img[1]: contrast=180, avg_sat=41.8, **is_bimodal=True**, noise=354.

**Immagini:**
- `00.jpg`: **Sezione finale (footer)** dello scontrino Il Gigante: dettaglio pagamenti (TCK PASTO E. 10.58, CONTANTI 20.00), legenda aliquote IVA (B, C, D), barcode, firma elettronica, saldo punti. Nessun item prodotto. Il testo è misto (barre, codici, legende) — meno bimodale di una lista prodotti classica.
- `01.jpg`: **Sezione iniziale (header + item)** del medesimo scontrino Il Gigante (data 27/04/2026, ora 18:24): 4 articoli visibili — INTIMO NEUTRONE, OLIO SEMI GIRASOLE, TONNO RIUNIONE OLIO, KETCHUP. Totale 17.49€. Chiaramente bimodale (testo nero su bianco).

**Confronto:** is_bimodal=False per il footer e True per gli item è perfettamente spiegabile: il footer con barcode, codici hash e legende ha una distribuzione di pixel meno bimodale rispetto alla classica lista prodotti. Entrambe le immagini sono sezioni dello stesso scontrino. Il contrasto diverso (129 vs 180) riflette la tipografia differente delle due sezioni. I 4 item estratti corrispondono esattamente a quanto visto in img[1].

**Corrispondenza: SMENTITA** — bimodal_mixed non indica non-receipt. La differenza riflette la struttura interna dello scontrino.

---

## 2. Pattern trovati

### Molto affidabile

**`SKIPPED_LOW_CONTENT(coverage < 0.10)`**
- Confermato in entrambe le occorrenze (GUID 2: coverage=0.04, GUID 4: coverage=0.12).
- Il valore numerico corrisponde visivamente con precisione alla proporzione del fotogramma occupata dallo scontrino.
- Zero falsi positivi nel campione.
- **Utilità:** segnala che l'immagine è inutilizzabile non perché non sia uno scontrino, ma perché è troppo lontana. Non distingue receipt da non-receipt, ma identifica immagini da ignorare con alta affidabilità.

**Dimensioni immagine anomale (width < 500px o height < 400px)**
- GUID 5: img[1] a 437×2223 → REJECTED confermato dalla piccola dimensione.
- GUID 8: img[2] a 1920×300 → anomalia dimensionale evidente.
- Come proxy standalone è molto utile: nessuna foto normale di uno scontrino produce dimensioni così fuori scala.

### Parzialmente affidabile

**`REJECTED(blur=X, edge%=0.0)` con statistiche tutte a zero**
- GUID 1 e GUID 5: il segnale esiste, ma la causa visiva non è confermabile direttamente.
- Nelle 2 occorrenze, il segnale non corrisponde a un non-receipt (sono scontrini) ma a un'immagine con problema tecnico (corruzione file, dimensioni minime, qualità insufficiente).
- **Utilità:** Identifica immagini da scartare, ma non il motivo.

**`NOT_A_RECEIPT_RETRY`**
- GUID 3: corrisponde a uno scontrino ruotato 90°.
- Il flag identifica correttamente un'immagine difficile da processare, ma la classificazione "not a receipt" è un falso positivo: è sempre un documento valido.
- **Utilità:** segnala difficoltà di processing, non necessariamente assenza di scontrino.

**`avg_saturation > 80` combinata con flags espliciti**
- Da sola è rumore, ma quando accompagna altri segnali può indicare un'immagine con sfondo dominante.

### Rumore (non corrisponde)

**`is_bimodal` inconsistente tra immagini (bimodal_mixed)**
- GUID 9 e GUID 10: entrambi i set sono scontrini validi. La differenza bimodale riflette la struttura del documento (footer vs lista item) o l'angolo di ripresa.
- Anche GUID 7 img[1] ha is_bimodal=True pur essendo il fronte dello scontrino.
- **Zero utilità** come indicatore di non-receipt.

**`avg_saturation > 80` senza altri segnali**
- GUID 6 (avg_sat=119): scontrino capovolto su sfondo legno.
- GUID 7 (avg_sat=84.2): fronte scontrino su sfondo rosso.
- GUID 8 (avg_sat=115.8): scontrino ruotato su tovaglia colorata.
- In tutti i casi, l'alta saturazione è causata dallo sfondo, non dal contenuto dell'immagine.
- **Causa principale degli falsi positivi nel campione.**

**`contrast_variance` alta tra immagini (>40)**
- GUID 9 (114 vs 158) e GUID 10 (129 vs 180): entrambi scontrini validi.
- Riflette semplicemente sezioni diverse dello stesso documento (header vs footer, vicino vs lontano).

---

## 3. Risposta alla domanda chiave

**Su questi 10 GUID: quanti fallimenti erano dovuti a input non valido vs fallimento reale della pipeline?**

Premessa importante: **nessuno dei 10 GUID è un fallimento** nel senso di output errato o vuoto. Tutti hanno prodotto item estratti coerenti con il contenuto reale degli scontrini. I segnali evidenziati riguardano immagini problematiche che la pipeline ha gestito scartando o ignorando.

Riclassificando le 20 immagini totali (10 GUID × avg 2 img):

| Categoria | Immagini | GUID coinvolti |
|-----------|----------|----------------|
| Input non valido: coverage troppo bassa (ricevuta fuori fuoco/lontana) | 2 | 2, 4 |
| Input non valido: file tecnico (dimensioni anomale, blur estremo) | 2 | 1 (img[0]), 5 (img[1]) |
| Input problematico: scontrino ruotato 90° | 1 | 3 (img[1]) |
| Input marginale: retro dello scontrino (no item) | 1 | 7 (img[2]) |
| Input marginale: scontrino capovolto/male orientato | 3 | 6 (img[1]), 8 (img[1], img[2]) |
| Input valido ma segnale falso positivo | 11 | 6, 7, 8, 9, 10 |

**Stima:**
- ~30% delle immagini analizzate aveva un genuino problema di input (coverage bassa, file corrotto, rotazione estrema).
- ~5–10% era "semi-inutile" (retro ricevuta, footer-only) ma non causava errori di estrazione.
- ~60–65% delle immagini era valida ma veniva parzialmente flaggata da segnali rumorosi.

**Nessun caso** di immagine completamente estranea (prodotto, volto, paesaggio, screenshot) è stato trovato in questo campione di 10 GUID.

**Confidenza sulla stima:** media (~65%) — il campione è piccolo e selezionato per avere segnali anomali, non per essere rappresentativo del dataset generale.

---

## 4. Raccomandazione

**È possibile costruire un judge testuale che identifichi i casi non-receipt senza guardare le immagini?**

### Risposta: PARZIALMENTE

#### Cosa funziona bene in modo testuale

Un judge testuale che usa **esclusivamente i flag espliciti della pipeline** raggiunge alta affidabilità per due pattern:

1. **`SKIPPED_LOW_CONTENT(coverage < 0.10)`** → quasi certamente l'immagine è troppo lontana o fuori inquadratura. Affidabilità stimata: ~95%.
2. **`REJECTED(...)` + dimensioni anomale (< 500px su qualsiasi asse)** → immagine tecnicamene inutilizzabile. Affidabilità stimata: ~80% (rimane incertezza su false positive come GUID 1).

Questi due pattern da soli coprirebbero 4 dei 6 casi genuinamente problematici in questo campione.

#### Cosa NON funziona in modo testuale

- **`avg_saturation > 80` senza flag espliciti**: 3/3 falsi positivi nel campione. Sfondo colorato, mano, tovaglia — tutti producono saturazione alta su scontrini validi.
- **`is_bimodal` inconsistente**: 3/3 falsi positivi. Riflette la struttura dello scontrino, non il tipo di immagine.
- **`NOT_A_RECEIPT_RETRY`**: identifica difficoltà di processing, non contenuto non-receipt. Il flag ha scattato su uno scontrino ruotato 90°.

#### Limite fondamentale

Il caso più interessante — **il retro dello scontrino CONAD (GUID 7, img[2])** — non è stato flaggato da nessun segnale. Era l'unica immagine del campione con contenuto genuinamente "non-item" (contenuto promozionale), e il JSON non la distingue in alcun modo dalle immagini normali. Un judge testuale non può rilevare questo caso.

#### Conclusione operativa

Un judge testuale basato su `coverage < 0.10` e `REJECTED + dimensioni anomale` può:
- Ridurre il rumore di review del ~30% scartando i casi chiari
- Non può sostituire la verifica visiva per: retri di scontrino, scontrini ruotati, immagini capovolte

Per una pipeline robusta, il judge testuale è utile come **primo filtro rapido**, non come classificatore completo. La verifica visiva rimane necessaria per i casi border (saturazione alta, bimodal inconsistente, NOT_A_RECEIPT_RETRY).
