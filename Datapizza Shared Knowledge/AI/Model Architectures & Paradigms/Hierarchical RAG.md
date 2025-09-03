L'idea alla base è di raffinare la costruzione dei chunk, quindi ogni chunk avrà la sottosezione del testo che ci interessa per rispondere.

**Nota:** è necessaria omogeneità architetturale tra i vari documenti e anche all'interno di essi, ovvero le sezioni in md devono avere tutte lo stesso livello, le sottosezioni uguale e così via. I file devono essere strutturati correttamente.

![[Pasted image 20250903114534.png]]

## Implementazione

Link: [https://git.datapizza.tech/lab-ai/datapizza/researchandevelopment/hierarchical-rag-exploration/-/tree/feature/main?ref_type=heads](https://git.datapizza.tech/lab-ai/datapizza/researchandevelopment/hierarchical-rag-exploration/-/tree/feature/main?ref_type=heads)

### Panoramica

L'Hierarchical RAG migliora i sistemi RAG tradizionali sfruttando la struttura gerarchica dei documenti. Invece di trattare i documenti come collezioni piatte di chunk, questa implementazione preserva le relazioni gerarchiche tra sezioni e sottosezioni, permettendo un recupero più contestuale e rilevante.

### Caratteristiche

![[Pasted image 20250903114558.png]]

- **Elaborazione Gerarchica dei Documenti**: Analizza i documenti preservando la loro struttura gerarchica, creando chunk arricchiti che contengono sia il testo della sottosezione che il contesto completo.
- **Metodi di Ingestione Multipli**:
    - `hierarchical`: Approccio gerarchico standard con informazioni su path e sezione
    - `summary`: Crea ed embedda i riassunti delle sezioni del documento
    - `r_hyde`: Usa Reverse Hypothetical Document Embeddings per generare e embeddare potenziali domande
- **Recupero Ibrido**:
    - Recupero denso tramite vettori (embeddings)
    - Recupero sparso tramite BM25 con lemmer italiano
- **Reranking Avanzato**:
    - Reranking basato su LLM
    - Reranking con Cohere
- **Elaborazione della Query**:
    - Riscrittura opzionale della query per migliorare il recupero
- Parsing delle tabelle e generazione di caption
- Estrazione di keyword dai documenti per migliorare il retrieve

### Componenti Tecnici

### Elaborazione Documenti

- Parsing gerarchico con preservazione della struttura
- Gestione delle tabelle con captioning AI-based
- Creazione di chunk aumentati con path, testo della sottosezione e testo completo

### Vector Storage

- Implementazione di vector store ibrido usando Qdrant
- Supporto sia per vettori densi che sparsi
- Meccanismo di caching efficiente per embeddings e indici BM25

### Retrieval

- Dense retrieval using text embeddings
- Sparse retrieval using BM25
- Hybrid retrieval combining both approaches
- Configurable threshold for relevance filtering

### Generazione della Risposta

- Generazione di risposte context-aware
- Attribuzione delle fonti per trasparenza
- Elaborazione parallela per operazioni batch

### Configurazione

Il sistema è altamente configurabile tramite un dizionario di configurazione:

```python
config = {
    'ingestion_type': 'hierarchical', # 'hierarchical', 'summary', o 'r_hyde'
    'retrieve_type': 'hybrid', # 'dense_bm25' o 'hybrid'
    'reranking': 'llm', # 'cohere', 'llm', o None
    'rewriting': True, # Se riscrivere le query
    'threshold': 0.7 # Soglia di rilevanza
}

```

### Pipeline

![[Pasted image 20250903114636.png]]


#dpKnowledge 