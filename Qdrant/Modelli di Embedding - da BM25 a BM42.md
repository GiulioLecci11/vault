#AI  
[[Embeddings & Context Window]]

Per usarlo: [https://huggingface.co/Qdrant/all_miniLM_L6_v2_with_attentions](https://huggingface.co/Qdrant/all_miniLM_L6_v2_with_attentions "https://huggingface.co/Qdrant/all_miniLM_L6_v2_with_attentions")
link articolo che parla di BM25: [https://www.arxiv.org/abs/2502.04645](https://www.arxiv.org/abs/2502.04645 "https://www.arxiv.org/abs/2502.04645")

BM42 si basa su SPLADE. Entrambi utilizzano dei modelli di AI per calcolare gli embeddings. Questi modelli sono "plug and play" nel senso che non prendono prompt o altro (non sono LLM anche se comunque si può estrarre un modello di embedding da un LLM (GTE) ma tutti quelli che usano modelli hanno il limite della context size)

Perché trainare un modello per embeddings se è circa un'operazione matematica tipo l'hash? Perché a differenza dell'hashing, che per parole semanticamente simili ritorna comunque stringhe di pari lunghezza ma diverse tra loro, un embedding fa operazioni matematiche complicatissime (i più inner degli hidden layers sono praticamente lingua degli dei) per trasformare parole in numeri ma mantenendo il significato.

## BM25: Il Fondamento della Ricerca

BM25 è una tecnica di embedding **SPARSE** basata su algoritmo che ha dominato i motori di ricerca per circa 40 anni. Non utilizza intelligenza artificiale, ma si basa su principi statistici. Il suo successo è dovuto principalmente a due componenti chiave:

1. **IDF (Inverse Document Frequency)**: La componente più importante della formula BM25. Misura quanto un termine è raro nell'intera collezione di documenti. Più un termine è raro (appare in pochi documenti), maggiore sarà il suo peso nel punteggio finale. L'IDF può essere interpretato come **l'importanza del termine all'interno del corpus**.
    
2. **Importanza del termine nel documento**: Questa parte valuta quanto un termine è rilevante all'interno di un singolo documento, basandosi su:
    
    - La frequenza del termine nel documento
    - La lunghezza relativa del documento rispetto alla media
    - Parametri come k1 e b che controllano l'influenza della frequenza del termine e della lunghezza del documento

BM25 funziona particolarmente bene per la ricerca web tradizionale, dove i documenti sono tipicamente lunghi (pagine web, articoli, libri) e l'importanza statistica dei termini può essere calcolata efficacemente.

## Limitazioni di BM25 per RAG

Con l'avvento dei sistemi RAG (Retrieval-Augmented Generation), alcuni presupposti di BM25 sono diventati problematici:

- I documenti in RAG sono generalmente più brevi (chunk) per permettere ai modelli densi di elaborarli e per identificare precisamente le parti rilevanti
- Con documenti più piccoli e a lunghezza fissa, la componente "importanza del termine nel documento" di BM25 diventa quasi inutile:
    - La frequenza del termine è generalmente 0 o 1
    - La lunghezza relativa del documento è sempre costante

Pertanto, in RAG, l'unica parte veramente utile di BM25 rimane l'IDF.

## SPLADE: Un'Alternativa Basata su AI

SPLADE è un approccio che utilizza modelli trasformer per generare rappresentazioni sparse dei testi. Invece di affidarsi a statistiche, SPLADE assegna pesi ai token tramite un modello addestrato end-to-end.

Nonostante SPLADE superi BM25 in alcuni benchmark, presenta diversi problemi:

- **Tokenizer inadeguato**: I tokenizer dei trasformer non sono progettati per compiti di recupero. Parole non presenti nel vocabolario vengono suddivise in sottounità o sostituite con [UNK]
- **Espansione dei token costosa**: Per compensare i problemi di tokenizzazione, SPLADE genera set di token simili, aumentando costi computazionali e di memoria
- **Dipendenza dal dominio e dalla lingua**: I modelli SPLADE sono addestrati su corpora specifici e non si generalizzano facilmente a nuovi domini
- **Tempi di inferenza**: I modelli SPLADE sono generalmente grandi e lenti, richiedendo GPU per un'inferenza rapida

## BM42: Il Meglio di Entrambi i Mondi

BM42 si basa su SPLADE ma cerca di combinare il meglio di BM25 e dei trasformer:

1. **Riutilizzo dell'IDF**: BM42 mantiene l'IDF di BM25, incorporando il calcolo direttamente nel motore Qdrant
    
2. **Attenzione invece di statistiche del documento**: Invece di usare statistiche sul documento (inadeguate per RAG), BM42 utilizza le matrici di attenzione dei modelli transformer:
    
    - Sfrutta l'attenzione dal token [CLS] verso gli altri token
    - Questo riflette l'importanza semantica di ciascun token per l'intero documento
    - È possibile estrarre queste informazioni da qualsiasi modello transformer senza addestramento aggiuntivo
3. **Ritokenizzazione WordPiece**: Per risolvere il problema della tokenizzazione:
    
    - Utilizza la tokenizzazione nativa del transformer per ottenere i vettori di attenzione
    - Inverte poi il processo, riunendo i sottotermini in parole complete
    - Applica tecniche NLP tradizionali come rimozione delle stopwords e lemmatizzazione ([[Lemming & Stemming NLP]])

La formula semplificata di BM42 diventa: Score(D,Q) = Somma per tutti i termini (IDF(termine) × Attenzione(CLS, termine)).

Nota che [CLS] è un token speciale utilizzato nei modelli transformer come BERT. Sta per "Classification" ed è un token che viene aggiunto all'inizio di ogni sequenza di input.

Questo token ha uno scopo particolare: durante l'addestramento del modello, la rappresentazione vettoriale finale del token [CLS] viene utilizzata per compiti di classificazione dell'intera sequenza. In pratica, il modello impara a condensare le informazioni rilevanti dell'intero testo in questo token speciale.

In BM42, l'attenzione che il token [CLS] rivolge agli altri token viene utilizzata come misura dell'importanza semantica di ciascun token per l'intero documento. Poiché [CLS] rappresenta l'intera sequenza, i pesi di attenzione tra [CLS] e gli altri token indicano quanto ciascun token contribuisce alla comprensione complessiva del testo.



BM42 utilizza modelli più leggeri e veloci rispetto a SPLADE (come sentence-transformers/all-MiniLM-L6-v2), mantenendo una buona qualità di recupero e supportando qualsiasi lingua per cui esista un modello transformer, senza necessità di addestramento specifico.

In sostanza, BM42 rappresenta un'evoluzione della ricerca lessicale che combina l'efficacia statistica di BM25 con la potenza semantica dei modelli AI, risolvendo molti problemi presenti nelle soluzioni precedenti.

