I **foundation models** sono modelli di grande scala, **pre-addestrati su ampi corpus di dati** e progettati per essere **riutilizzabili e adattabili** a una vasta gamma di compiti a valle (downstream tasks), spesso tramite fine-tuning supervisionato o tecniche più leggere come prompting.

---

## Caratteristiche principali dei Foundation Models

### 1. Pretraining su larga scala

- Addestrati su dataset vasti e generalisti (es. testo da web, codice, immagini).
- Obiettivo: apprendere rappresentazioni utili **senza supervisione specifica** (es. language modeling, masked prediction).

### 2. Modularità e riutilizzo

- Possono essere adattati a molti compiti (classificazione, generazione, traduzione, ecc.) con poco sforzo.
- Supportano paradigmi come **few-shot**, **zero-shot** e **fine-tuning supervisionato**.

### 3. Trasferibilità

- Le conoscenze acquisite nel pretraining vengono trasferite a nuovi task.
- Questo riduce il bisogno di grandi dataset etichettati per ciascun compito specifico.

---

## Esempi di Foundation Models

|Nome Modello|Tipo|Framework|Azienda/Organizzazione|
|---|---|---|---|
|GPT (2/3/4/5)|Testo|Transformer decoder|OpenAI|
|BERT|Testo|Transformer encoder|Google|
|CLIP|Visione + Testo|Dual encoder|OpenAI|
|DINO / SAM|Visione|Self-supervised|Meta AI|
|PaLM|Multilingua|Transformer|Google DeepMind|
|LLaMA|Testo|Transformer|Meta|
|Flamingo|Multimodale (testo+immagini)|Perceivers|DeepMind|

---

## Implicazioni e sfide

### Vantaggi

- Riutilizzabilità massiva.
- Riduzione del bisogno di labeled data.
- Prestazioni SOTA in molti task NLP, CV, multimodali.

### Rischi

- **Bias e fairness:** i modelli riflettono i dati con cui sono stati addestrati.
- **Costi computazionali:** addestramento estremamente dispendioso.
- **Mancanza di trasparenza:** difficile interpretare cosa il modello "sa".

#dpKnowledge 