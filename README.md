# Terminal Inventario Lamiere

Applicazione web single-page (`index.html`) per la gestione in tempo reale della giacenza lamiere e dello storico movimenti in officina. Progettata per uso touch/desktop.
---

## Caratteristiche Principali

* **Autenticazione & Ruoli (Supabase Auth)**:
  * **Operatore**: accesso completo a carico, scarico, creazione ed eliminazione schede.
  * **Viewer**: interfaccia in sola lettura con occultamento automatico delle azioni di modifica.
* **Sicurezza a Livello Database (RLS)**:
  * Politiche Row Level Security attive su Supabase. La scrittura è vincolata al metadato `operator` (`(auth.jwt() -> 'user_metadata' ->> 'role') = 'operator'`), garantendo la sicurezza dei dati anche con repository pubblica.
* **Gestione Giacenze Avanzata**:
  * Supporto alle quantità **decimali** (es. `0.5`, `1.25` fogli) gestite tramite tipo `numeric`/`float8`.
  * **Ordinamento deterministico fisso** (`materiale`, `spessore_mm`, `formato`, `id`) per evitare lo spostamento visivo delle righe durante i clic rapidi su `+1` / `-1`.
  * Evidenziazione automatica per materiale esaurito (`quantita = 0`).
* **Filtri & Ricerca Rapida**:
  * Campo di ricerca ottimizzato con filtro ristretto a **Materiale**, **Spessore** e **Note** (esclusi formato e quantità per evitare falsi positivi).
  * Selezione rapida del materiale tramite autocompletamento con tasto `Invio` (es. digiti `fe` + Invio $\rightarrow$ filtra *Ferro*).
* **Calcolo Pesi in Tempo Reale**:
  * Calcolo dinamico del peso singolo e totale per riga basato sulle densità specifiche dei materiali (Ferro/Zincato `7.85`, Inox `7.93`, Alluminio `2.70` kg/dm³).
* **Reportistica & Storico**:
  * Registro degli ultimi 50 movimenti (carico/scarico, operatore, variazione e saldo).
  * Esportazione del report di magazzino in **PDF** formattato tramite `html2pdf.js` con un solo clic.

---

## Tech Stack

* **Frontend**: HTML5, CSS3 (Custom Terminal Dark Theme), JavaScript Vanilla (ES6+).
* **Backend & DB**: [Supabase](https://supabase.com/) (PostgreSQL + Auth + Row Level Security).
* **Librerie Client**:
  * `@supabase/supabase-js` v2
  * `html2pdf.js` v0.10.1
* **Hosting**: GitHub Pages.

---

## 🗄️ Struttura Database (PostgreSQL / Supabase)

### Tabella `inventario`
| Colonna | Tipo | Note |
| :--- | :--- | :--- |
| `id` | `bigint` / `primary key` | Identificativo univoco |
| `materiale` | `text` | Ferro, Inox, Alluminio, Zincato |
| `spessore_mm` | `numeric` / `float8` | Spessore in millimetri |
| `formato` | `text` | Dimensioni (es. `3000x1500`) |
| `quantita` | `numeric` / `float8` | Giacenza disponibile |
| `note` | `text` | Note opzionali (es. pellicolato, fiorettato, traforato) |
| `updated_at` | `timestamptz` | Data e ora ultimo aggiornamento |

### Tabella `storico_movimenti`
| Colonna | Tipo | Note |
| :--- | :--- | :--- |
| `id` | `bigint` / `primary key` | Identificativo univoco |
| `lamiera_id` | `bigint` | FK riferita a `inventario.id` |
| `materiale` | `text` | Copia dettagli per tracciabilità |
| `spessore_mm` | `numeric` | Copia dettagli per tracciabilità |
| `formato` | `text` | Copia dettagli per tracciabilità |
| `tipo_operazione` | `text` | `CARICO` o `SCARICO` |
| `quantita_variata` | `numeric` | Variazione apportata (+/-) |
| `quantita_risultante`| `numeric` | Saldo finale post-variazione |
| `operatore` | `text` | Email dell'utente che ha eseguito l'azione |
| `created_at` | `timestamptz` | Timestamp automatico dell'operazione |

---

## ⚙️ Setup & Deployment

1. **GitHub Pages**
2. **Supabase**
