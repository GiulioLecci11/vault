#AI 
[[Modelli di Embedding - da BM25 a BM42]]
[[Vector Databases]]

# Strategie di Retrieval in ambito RAG

Le strategie di retrieval rappresentano un elemento chiave nei sistemi RAG (Retrieval-Augmented Generation). Ecco un'analisi approfondita dei diversi approcci:

## Strategie di Base nel Retrieval

### Dense Retrieval

Il **dense retrieval** utilizza embedding vettoriali densi (tanti numeri diversi da 0) per rappresentare documenti e query:

- **Funzionamento**: Trasforma testi in vettori di alta dimensionalità usando modelli come BERT, Sentence-BERT o modelli proprietari
- **Vantaggi**:
    - Cattura ***relazioni semantiche*** tra concetti anche se usano vocabolari diversi
    - Efficace nella comprensione del contesto e significati impliciti
    - Migliore generalizzazione su domini specifici
- **Limitazioni**:
    - Computazionalmente più costoso
    - Richiede modelli pre-addestrati di alta qualità
    - Può avere difficoltà con termini molto tecnici o specifici

### Sparse Retrieval (Keyword-based)

Il **sparse retrieval** si basa su rappresentazioni sparse (moltissime componenti del vettore sono pari a 0) di parole chiave:

- **Funzionamento**: Utilizza metodi come TF-IDF o BM25 che considerano frequenza e rarità dei termini
- **Vantaggi**:
    - Computazionalmente più leggero
    - Molto efficace per corrispondenze esatte di termini
    - Non richiede addestramento specifico
    - Eccellente per termini tecnici, nomi propri, numeri
- **Limitazioni**:
    - Manca comprensione semantica
    - Fallisce con sinonimi o parafrasi
    - Sensibile a errori di battitura

### Retrieval Ibrido

L'approccio **ibrido** combina i vantaggi di entrambi:

- **Funzionamento**: Esegue sia retrieval sparso che denso, poi fonde i risultati
- **Strategie di fusione**:
    - RRF (Reciprocal Rank Fusion)
    - Ponderazione dinamica basata sul tipo di query
    - Re-ranking: prima retrieval veloce con metodi sparsi, poi raffinamento con metodi densi
- **Vantaggi**:
    - Maggiore robustezza
    - Bilancia precisione semantica e corrispondenza esatta
    - Mitiga i punti deboli di ciascun approccio
- **Quando usarlo**: Ideale per sistemi di produzione dove è necessaria alta precisione e recall

## Strategie RAG Avanzate

### Graph RAG

**Graph RAG** incorpora strutture grafiche per migliorare il retrieval:

- **Funzionamento**: Rappresenta documenti e relazioni in un grafo della conoscenza
- **Caratteristiche**:
    - I documenti sono nodi
    - Le relazioni semantiche sono archi
    - Utilizza algoritmi di attraversamento del grafo per il retrieval
- **Vantaggi**:
    - Cattura relazioni complesse tra documenti
    - Facilita reasoning multi-hop
    - Permette query basate su relazioni
- **Casi d'uso**: Knowledge-intensive tasks, reasoning su domini complessi interconnessi

### Agentic RAG

**Agentic RAG** introduce agenti autonomi nel processo di retrieval:

- **Funzionamento**: Agenti intelligenti gestiscono dinamicamente il processo di retrieval
- **Caratteristiche**:
    - Riformulazione iterativa delle query
    - Decomposizione di query complesse in sotto-query
    - Interpretazione dei risultati e feedback loop
- **Vantaggi**:
    - Gestisce query complesse attraverso decomposizione
    - Si adatta dinamicamente ai risultati intermedi
    - Può integrare fonti multiple
- **Casi d'uso**: Query complesse, ricerca multi-step, investigazioni approfondite

### Hierarchical RAG

**Hierarchical RAG** organizza informazioni in strutture gerarchiche:

- **Funzionamento**: Crea livelli di astrazione sui documenti (sommari, dettagli)
- **Caratteristiche**:
    - Rappresentazione multi-livello dei documenti
    - Retrieval progressivo dal generale al dettagliato
    - Indicizzazione gerarchica
- **Vantaggi**:
    - Gestisce efficacemente grandi corpora
    - Migliora l'efficienza computazionale
    - Permette navigazione contestuale
- **Casi d'uso**: Documentazioni tecniche estese, knowledge base organizzate

### Recursive RAG

**Recursive RAG** applica il retrieval in modo ricorsivo:

- **Funzionamento**: I risultati di una query diventano input per query successive
- **Vantaggi**:
    - Esplorazione approfondita di un argomento
    - Raffinamento progressivo dei risultati
    - Migliora il reasoning complesso
- **Casi d'uso**: Analisi approfondita, investigazioni a cascata

## Confronto tra Approcci di Retrieval

### Dense vs Sparse Vectors: Quando Usarli

**Dense Vectors sono preferibili quando**:

- La comprensione semantica è cruciale
- Le query contengono parafrasi o sinonimi
- Si lavora con domande in linguaggio naturale
- È importante la generalizzazione su domini specifici

**Sparse Vectors sono preferibili quando**:

- La precisione terminologica è fondamentale
- Si cercano entità specifiche, numeri o codici
- La velocità di retrieval è prioritaria
- Si hanno limitate risorse computazionali

**L'approccio ibrido è consigliato quando**:

- Si necessita di alta robustezza in produzione
- Il corpus è eterogeneo (tecnico e discorsivo)
- Si vuole bilanciare precisione e recall
- È importante gestire diverse tipologie di query

## Implementazioni e Considerazioni Pratiche

### Ottimizzazione del Dense Retrieval

- **Compressione**: Tecniche come PQ (Product Quantization) per ridurre dimensionalità
- **Chunking strategico**: Divisione dei documenti in frammenti semanticamente coerenti
- **Fine-tuning degli embedding**: Per domini specifici

### Ottimizzazione del Sparse Retrieval

- **Normalizzazione terminologica**: Lemmatizzazione, stemming
- **Espansione sinonimi**: Arricchimento automatico con thesauri
- **Ponderazione personalizzata**: Aggiustamento dei pesi per termini specifici del dominio

### Metriche di Valutazione

- **MRR (Mean Reciprocal Rank)**: Valuta la posizione del primo risultato rilevante
- **NDCG (Normalized Discounted Cumulative Gain)**: Considera l'ordinamento dei risultati rilevanti
- **Precision/Recall**: Bilancio tra precisione e completezza

Le strategie RAG avanzate continuano ad evolversi, con approcci che combinano sempre più tecniche di AI, come rewriting di query, relevance filtering, e modelli contestuali che adattano dinamicamente la strategia di retrieval in base al tipo di informazione cercata e al contesto della richiesta.