Fase di addestramento in cui il modello impara a prevedere il **prossimo token** in base al contesto.

## Dati:

- Centinaia di miliardi di token (Wikipedia, CommonCrawl...).
- **UNSUPERVISED DATA** (no labels)

## Output:

- [Foundation Models](https://www.notion.so/Foundation-Models-24e04d204fa28042b89feaa458a4b495?pvs=21) come GPT-3, LLaMA, Falcon.

```
Input: "Il gatto sta dorm"
Output atteso: "endo"

```

### Obiettivo del Pretraining

Nel caso del **Causal Language Modeling (CLM)** (usato da GPT), l’obiettivo è predire ogni token dato il contesto precedente.

**Input:** `["The", "Ġcat", "Ġsat"]`

**Target:** `["Ġcat", "Ġsat", "Ġon"]`

Il modello deve predire il token successivo per ogni posizione nella sequenza.

---

## Fasi

### 1. Forward Pass

1. Ogni token è convertito in un **embedding vettoriale**.
2. Gli embedding sono passati attraverso una rete **Transformer**, composta da:
    - Self-attention
    - Feedforward layers
    - Layer normalization
3. Il modello produce una **distribuzione di probabilità** sui token del vocabolario per ogni posizione.

---

### 2. Calcolo della Loss

La **loss** usata è la **cross-entropy**, che misura la distanza tra la distribuzione predetta $\hat{y}$ e la distribuzione vera $y$:

$$

\mathcal{L}t = -\sum{i} y_t^{(i)} \log(\hat{y}_t^{(i)})

$$

La loss totale sulla sequenza di lunghezza \( T \) è:

$$

\mathcal{L} = \frac{1}{T} \sum_{t=1}^{T} \mathcal{L}_t

$$

---

### 3. Backpropagation

Si calcola il gradiente della loss rispetto ai pesi del modello, usando la regola della catena (chain rule). Questo processo si chiama **backpropagation**.

$$

\frac{\partial \mathcal{L}}{\partial W}

$$

dove \( W \) rappresenta i pesi dei vari layer della rete.

---

### 4. Aggiornamento dei Pesi

I pesi vengono aggiornati con un **ottimizzatore** (tipicamente Adam o AdamW):

$$

W \leftarrow W - \eta \cdot \frac{\partial \mathcal{L}}{\partial W}

$$

dove $\eta$ è il **learning rate**.

---

### 5. Ciclo di Apprendimento

Questo processo (forward → loss → backward → update) viene ripetuto su milioni (o miliardi) di sequenze testuali, organizzate in batch.

Il modello:

- Riduce la loss a ogni iterazione
- Migliora la sua capacità di predizione
- Impara rappresentazioni linguistiche sempre più sofisticate

---

### Cosa Impara il Modello?

- **Layer inferiori** → sintassi, concordanze grammaticali, struttura delle frasi
- **Layer medi** → significati di frasi, relazioni semantiche
- **Layer superiori** → ragionamento, conoscenza del mondo, risposte complesse

---

### Fine del Pretraining

Il pretraining termina quando:

- Si raggiunge un numero massimo di iterazioni (o epoche)
- La loss si stabilizza su un valore accettabile
- Eventualmente, si osserva la **perplessità (perplexity)** come metrica ausiliaria

Il modello può poi essere:

- Usato direttamente (zero-shot o few-shot)
- Affinato su task specifici (fine-tuning supervisionato)

---

## Riepilogo

| Fase            | Descrizione                                        |
| --------------- | -------------------------------------------------- |
| Tokenizzazione  | Conversione del testo in token                     |
| Forward Pass    | Calcolo delle predizioni con la rete neurale       |
| Loss            | Calcolo dell'errore tramite cross-entropy          |
| Backpropagation | Propagazione dell'errore per calcolare i gradienti |
| Update          | Aggiornamento dei pesi del modello                 |
| Ripetizione     | Iterazione del processo su grandi dataset          |

#dpKnowledge 