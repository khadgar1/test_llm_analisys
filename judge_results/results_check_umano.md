# Risultati — Check Umano (9 GUID)

**Data:** 2026-05-13 | **Metodo:** Visione immagine scontrino + analisi JSON  
**Fascia confidenza:** 31–79 (zona ambigua del pipeline)

---

## Sommario

| Classificazione | Conteggio | % |
|-----------------|-----------|---|
| `brand_collision` | **6** | 66.7% |
| `variante_incerta` | **3** | 33.3% |
| `legittimo` | 0 | 0.0% |

---

## Dettaglio per GUID

### 1. 69e61bea551a2cb157c09a0d — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | CONAD |
| Item scontrino | `PATATINE CHEF GOURME` |
| Prodotto catalogo | Panna 200 ml — **Chef** |
| Confidenza | 77 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino CONAD girato al contrario. Voce "PATATINE CHEF GOURM" visibile chiaramente a vari prezzi — si tratta di patatine/chips. Il termine "Chef" nella descrizione è un aggettivo di qualità ("Chef Gourmet"), non il brand Chef che produce panna cucina.

---

### 2. 69ecef9a86e5b64551ba4957 — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | Decò Supermercati |
| Item scontrino | `FINI COTTO LUSSO` |
| Prodotto catalogo | Tortelloni Ricotta E Spinaci 250g — **Fini** |
| Confidenza | 79 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino Decò. "FINI COTTO LUSSO" è Fini brand prosciutto cotto (salume). Il catalogo ha Fini pasta/tortelloni. Stesso brand, categoria di prodotto completamente diversa (salumi vs. pasta fresca). Il pipeline ha matched sul brand Fini ignorando il tipo prodotto.

---

### 3. 69e72adc86e5b64551ba0b61 — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | EUROSPAR |
| Item scontrino | `PASTA SEM.GAROFALO` (×3) |
| Prodotto catalogo | Ricotta di Bufala Campana DOP — **Fattorie Garofalo** |
| Confidenza | 77 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino EUROSPAR, girato su sfondo floreale. "PASTA SEM.GAROFALO" appare più volte — Pasta Semola Garofalo (brand pasta). Il catalogo ha Fattorie Garofalo (azienda lattiero-casearia campana) che produce ricotta. Due aziende distinte omonime.

---

### 4. 69ed18c086e5b64551ba4ace — VARIANTE INCERTA
| Campo | Valore |
|-------|--------|
| Store | SUPERMERCATO VIVO |
| Item scontrino | `RED BULL LATT.CL25` (×2) |
| Prodotto catalogo | Red Bull Green Edition Gusto Dragon Fruit 250 ml — **Red Bull** |
| Confidenza | 79 |
| **Classificazione** | **variante_incerta** |

**Analisi immagine:** Scontrino VIVO. "RED BULL LATT.CL25" a 1.59€ × 2. Brand e taglia corrispondono (250ml). Impossibile determinare dal testo scontrino se si tratta della variante standard o Dragon Fruit Green Edition.

---

### 5. 69e8b7b82bfcf6b2e63115f8 — VARIANTE INCERTA
| Campo | Valore |
|-------|--------|
| Store | Superò |
| Item scontrino | `ZYMIL UHT P.S. ML.250` |
| Prodotto catalogo | Magro Digeribile 250 ml — **Zymil** |
| Confidenza | 74 |
| **Classificazione** | **variante_incerta** |

**Analisi immagine:** Scontrino Superò (Napoli). "ZYMIL UHT P.S. ML.250" a 0.88€ visibile. P.S. = Parzialmente Scremato. Il catalogo ha Zymil Magro Digeribile 250ml. Stesso brand e formato, diverso tenore di grasso (Parzialmente Scremato vs. Magro/0% grasso). Potrebbe essere la stessa referenza o una variante.  
*Nota: stesso scontrino ha anche "CAVOLO CAPPUC B/R" → Candeggina Omino Bianco (barcode collision separato, conf=74).*

---

### 6. 69eb4ffa3da34a89f70540e4 — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | SUPERMERCATO TOSANO |
| Item scontrino | `CUORE DI SUINO CONF` |
| Prodotto catalogo | Cuore Sfoglie al forno con farina di Fagioli e di grano saraceno — **Cuore** |
| Confidenza | 79 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino TOSANO (PD). "CUORE DI SUINO CONF" visibile verso il fondo — cuore anatomico di suino (prodotto carne/frattaglie). Il brand "Cuore" produce sfoglie/cracker al forno (snack). La parola "cuore" nel nome della carne ha triggerato il brand Cuore.

---

### 7. 69e9db8b2bfcf6b2e6311f7c — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | SUPERConveniente |
| Item scontrino | `TERRABUONA PISELLI FINI` |
| Prodotto catalogo | Ravioli Salsiccia E Friarielli 250g Le Ricette Italiane — **Fini** |
| Confidenza | 76 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino lunghissimo (164.27€) con nota scritta a mano "Concorso Ace 30 Aprile Galbani...". "TERRABUONA PISELLI FINI" = Terrabuona brand piselli fini (piselli di qualità). L'aggettivo "fini" (= di qualità, finemente lavorati) ha matched con il brand Fini (pastificio).

---

### 8. 69ef509586e5b64551ba5b38 — BRAND COLLISION
| Campo | Valore |
|-------|--------|
| Store | (FIDATY loyalty program) |
| Item scontrino | `AMARO JAGERMEISTER` |
| Prodotto catalogo | Amaro Lucano 70cl — **Amaro Lucano** |
| Confidenza | 67 |
| **Classificazione** | **brand_collision** |

**Analisi immagine:** Scontrino con programma fedeltà FIDATY. Voce "JAGERMEISTER" visibile. Jägermeister è un digestivo tedesco a base di erbe completamente diverso dall'Amaro Lucano (digestivo italiano). L'unico elemento in comune è la parola generica "amaro" (= digestivo amaro). La confidenza bassa (67) riflette correttamente l'incertezza.

---

### 9. 69eaf7982bfcf6b2e6312aae — VARIANTE INCERTA
| Campo | Valore |
|-------|--------|
| Store | CRAI |
| Item scontrino | `RED BULL LATT 25CL` |
| Prodotto catalogo | Red Bull Green Edition Gusto Dragon Fruit 250 ml — **Red Bull** |
| Confidenza | 79 |
| **Classificazione** | **variante_incerta** |

**Analisi immagine:** Scontrino CRAI. "RED BULL LATT 25CL" visibile. Stesso scenario del GUID 69ed18c: brand e taglia corrispondono (250ml), flavor non distinguibile dal testo scontrino. Pattern ricorrente: Red Bull Green Edition Dragon Fruit è l'unico Red Bull 250ml nel catalogo promozioni.

---

## Pattern Identificati

### Brand Collision (6/9 = 66.7%)
Tutte le 6 brand collision condividono lo stesso meccanismo: **una parola comune nella descrizione scontrino coincide con un brand name nel catalogo**, ma la parola è usata in senso diverso:

| Parola trigger | Senso nello scontrino | Brand nel catalogo |
|---------------|----------------------|---------------------|
| Chef | Aggettivo qualità (Chef Gourmet patatine) | Chef (panna cucina) |
| Fini | Brand salumi (Fini Cotto Lusso) | Fini (pastificio) — stesso brand, cat. diversa |
| Garofalo | Brand pasta (Pasta Garofalo) | Fattorie Garofalo (formaggi) — aziende diverse |
| Cuore | Anatomico (cuore di suino) | Cuore (snack brand) |
| Fini | Aggettivo (piselli fini = di qualità) | Fini (pastificio) |
| Amaro | Categoria bevanda (amaro Jägermeister) | Amaro Lucano (brand specifico) |

### Variante Incerta (3/9 = 33.3%)
Tutti e 3 i casi `variante_incerta` coinvolgono situazioni dove:
- Brand corretto ✓
- Taglia corretta ✓  
- Flavor/variante non verificabile dal testo scontrino abbreviato ✗

Casi: Red Bull standard vs Dragon Fruit (×2), Zymil PS vs Magro.

### Implicazione per il pipeline
La zona di confidenza 31–79 non contiene **nessun match legittimo** nel campione. Questo suggerisce che la soglia 80 sia correttamente calibrata come cut-off per i match validi.
