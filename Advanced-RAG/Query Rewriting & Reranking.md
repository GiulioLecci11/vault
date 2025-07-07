#AI 
[[RAG Concepts]]

I sistemi RAG (Retrieval-Augmented Generation) rappresentano una delle architetture più potenti per potenziare i modelli linguistici con conoscenze esterne. Due componenti fondamentali che migliorano significativamente le prestazioni di questi sistemi sono il query rewriting e il reranking.

## Query Rewriting

Il query rewriting è il processo di trasformazione della query originale dell'utente in una forma più efficace per il recupero delle informazioni.

### Tecniche principali:

1. **Espansione della query**: Arricchisce la query con termini correlati.
    
    - Esempio: "trattamento diabete" → "trattamento diabete tipo 1 tipo 2 insulina metformina glicemia"
2. **Riformulazione**: Riscrive completamente la query mantenendone il significato.
    
    - Esempio: "Quali sono gli effetti collaterali dell'aspirina?" → "Aspirina effetti negativi rischi controindicazioni"
3. **Scomposizione in sotto-query**: Divide query complesse in componenti più semplici.
    
    - Per la domanda "Confronta i vantaggi dei veicoli elettrici con quelli a combustione interna", potrebbe generare:
        - "Vantaggi dei veicoli elettrici"
        - "Vantaggi dei veicoli a combustione interna"
        - "Confronto tra veicoli elettrici e a combustione"
4. **Generazione di query ipotetiche**: Crea possibili domande correlate al tema.
    
    - Esempio: Per "Investimenti sostenibili", genera "Cosa sono gli investimenti ESG?", "Quali sono i criteri per definire un investimento sostenibile?", etc.

### Implementazioni:

- **Approcci basati su LLM**: Utilizzando prompt come "Riscrivi questa query per migliorare il recupero di documenti: [query]"
- **Modelli specializzati**: Addestrati specificamente per il rewriting (es. HyDE - Hypothetical Document Embeddings)
- **Tecniche ibride**: Combinazione di metodi statistici classici (TF-IDF) con approcci neurali

## Reranking

Il reranking consiste nella riorganizzazione dei documenti recuperati in base alla loro rilevanza rispetto alla query originale. Viene eseguito dopo il recupero iniziale.

### Tecniche principali (prova Cohere):

1. **Cross-Encoder**: Valutano contemporaneamente la query e il documento.
    
    - Più accurati ma computazionalmente più costosi
    - Esempi: BERT, RoBERTa o modelli specializzati come MS MARCO
2. **Approcci basati su LLM**:
    
    - Utilizzo di modelli come GPT o Claude per valutare la rilevanza
    - Possono dare punteggi o classificare direttamente i documenti
3. **Reranking multi-stadio**:
    
    - Prima fase: filtro rapido con modelli leggeri
    - Seconda fase: analisi approfondita con modelli più sofisticati
4. **Reranking semantico**:
    
    - Considerazione del contesto e dell'intento dell'utente
    - Valutazione della coerenza tematica e concettuale

### Metriche di valutazione:

- **Precision@K**: Percentuale di documenti rilevanti tra i primi K risultati
- **nDCG (Normalized Discounted Cumulative Gain)**: Misura l'utilità basata sulla posizione e rilevanza
- **MRR (Mean Reciprocal Rank)**: La media dell'inverso della posizione del primo documento rilevante

## Integrazione nel flusso RAG

In un sistema RAG completo, il flusso tipico è:

1. Query originale dell'utente
2. **Query rewriting** per migliorare l'efficacia del recupero
3. Recupero di documenti candidati (usando embedding o metodi ibridi)
4. **Reranking** dei risultati per prioritizzare i più rilevanti
5. Generazione della risposta finale basata sui documenti selezionati

## Vantaggi dell'integrazione

- **Miglioramento della coverage**: Il query rewriting consente di affrontare il vocabolario mismatch
- **Aumento della precisione**: Il reranking migliora la qualità dei documenti utilizzati per la generazione
- **Riduzione delle allucinazioni**: Documenti più pertinenti riducono la necessità di "inventare" risposte
- **Ottimizzazione delle risorse**: Approcci a due stadi permettono di bilanciare velocità e accuratezza

## Sfide attuali

- **Bilanciamento tra diversità e rilevanza**: Garantire che i documenti recuperati siano sia rilevanti che diversificati
- **Efficienza computazionale**: Specialmente per il reranking con cross-encoder
- **Valutazione della rilevanza contestuale**: Considerare non solo la corrispondenza lessicale ma anche quella semantica
- **Personalizzazione**: Adattare i sistemi alle esigenze e al contesto specifico dell'utente

L'efficacia di un sistema RAG dipende fortemente da come vengono implementati questi componenti e da come interagiscono tra loro nel flusso complessivo di elaborazione delle informazioni.

NOTE
Sulla base di cosa capisco come fare reranking e come fare query rewriting? Cioè come so qual è il modo ottimale di scrivere query/ rerankare documenti?

Per il rewriting la cosa va molto trial and error, in generale il rewriter fa da tappabuchi alla ricerca semantica per similarità (sempre perché l'embedder fa schifo). Per esempio ci si può accorgere che a seguito di parole molto specifiche di dominio (come per esempio sigle) la ricerca semantica esploda. Potrebbe essere utile specificare nel system prompt (il rewriter è sempre un llm) che a certe sigle va aggiunte la sigla per esteso ecc ecc..

Per quanto riguarda il reranking invece, spesso si usa un llm come 4o-mini (anche se, come i modelli embedder che poi sono encoders, esistono dei modelli specifici che sono solo reranker e sarebbero cross encoders). Di solito fai un retrieval che prende tanti documenti e prendere tante robe (anche falsi positivi) e poi restringi con un reranker più intelligente (sempre perché il modello embedder che poi è quello che si usa per la prima ricerca di similarità è stupido)