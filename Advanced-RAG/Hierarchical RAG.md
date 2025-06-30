[[RAG Concepts]]
#AI  
Il Hierarchical RAG (Retrieval-Augmented Generation gerarchico) rappresenta un'evoluzione significativa rispetto ai sistemi RAG tradizionali, introducendo una struttura a più livelli nel processo di recupero e generazione delle informazioni.

## Concetto Fondamentale

L'idea centrale del Hierarchical RAG è organizzare sia il processo di recupero che la base di conoscenza in strutture gerarchiche, permettendo un accesso alle informazioni più preciso, efficiente e contestualizzato.

## Architettura Tipica

### 1. Organizzazione Gerarchica dei Documenti

I documenti vengono organizzati in strutture multi-livello:

- **Livello Macro**: Documenti interi, capitoli, o sezioni principali
- **Livello Intermedio**: Paragrafi, sottosezioni tematiche
- **Livello Micro**: Frasi, fatti specifici, entità

Questa strutturazione consente di accedere alle informazioni con diversi livelli di granularità.

### 2. Retrieval Gerarchico

Il processo di recupero avviene attraverso passaggi successivi:

1. **Retrieval di Primo Livello**: Identifica i documenti o le sezioni più rilevanti per la query
2. **Retrieval di Secondo Livello**: All'interno dei documenti selezionati, individua i paragrafi o sottosezioni più pertinenti
3. **Retrieval di Dettaglio**: Estrae informazioni specifiche, fatti, o frasi rilevanti dai contenuti selezionati

### 3. Contestualizzazione Progressiva

Ogni livello di recupero arricchisce il contesto per il livello successivo:

- I documenti di primo livello forniscono il contesto generale
- I paragrafi intermedi aggiungono il contesto tematico specifico
- Le informazioni dettagliate completano il quadro con fatti puntuali

## Implementazioni e Varianti

1. **Hierarchical Dense Retrieval (HDR)**
    
    L'HDR utilizza embedding diversi per ogni livello gerarchico, distinguendo tra rappresentazioni globali e locali:
    
    - **Embedding globali**:
        - Catturano il significato complessivo e i temi principali di interi documenti o sezioni ampie
        - Sono ottimizzati per individuare la rilevanza tematica generale
        - Hanno dimensionalità spesso maggiore per rappresentare concetti più ampi
        - Esempio: un embedding globale di un articolo scientifico catturerà i concetti principali come "intelligenza artificiale", "deep learning", "neural networks"
    - **Embedding locali**:
        - Rappresentano dettagli specifici, frasi chiave, o fatti puntuali
        - Sono più sensibili a termini specifici e informazioni granulari
        - Possono essere addestrati con funzioni di perdita che enfatizzano la precisione sui dettagli
        - Esempio: un embedding locale catturerà informazioni come parametri specifici, risultati numerici, o definizioni precise
    
    La combinazione di questi due tipi permette sia una ricerca ampia iniziale che un affinamento progressivo.
    
2. **Tree-of-Thought RAG**
    
    Questa variante combina l'approccio "Tree of Thought" con il RAG gerarchico attraverso questi passaggi:
    
    - Formula diverse interpretazioni della query dell'utente
    - Per ciascuna interpretazione, esplora percorsi di recupero diversi
    - Valuta i risultati di ogni percorso in base a metriche come coerenza, completezza e rilevanza
    - Seleziona il percorso migliore o integra informazioni da percorsi multipli
    
    Questo approccio è particolarmente efficace per query ambigue o multifaceted.
    
3. **Recursive Retrieval**
    
    Il retrieval ricorsivo funziona attraverso un ciclo di feedback continuo:
    
    - Recupera documenti di alto livello utilizzando la query originale
    - Analizza i documenti recuperati per generare query più specifiche
    - Utilizza queste nuove query per recuperare contenuti più dettagliati
    - Ripete il processo fino a raggiungere il livello di dettaglio desiderato o fino al soddisfacimento di criteri di stop
    
    È particolarmente utile quando l'utente inizia con una query generale che necessita di specializzazione progressiva.
    
4. **Semantic Decomposition RAG**
    
    Questo approccio scompone semanticamente la query in componenti gerarchiche:
    
    - Identifica il tema principale della query
    - Scompone il tema in sottotemi o aspetti correlati
    - Per ogni sottotema, genera query specializzate
    - Recupera informazioni a ciascun livello di questa decomposizione semantica
    
    Particolarmente efficace per query complesse che toccano diversi aspetti di un argomento.
    
5. **Hierarchical Graph-based RAG**
    
    Utilizza strutture di grafo per rappresentare le relazioni gerarchiche:
    
    - Ogni nodo rappresenta un'entità o un concetto a diversi livelli di astrazione
    - I collegamenti tra nodi rappresentano relazioni semantiche o tematiche
    - Il retrieval avviene navigando il grafo dai concetti più generali ai più specifici
    - Consente di seguire percorsi di connessione tra idee correlate attraverso la gerarchia

## Approfondimento su Embedding Globali e Locali

### Principi di Funzionamento

Gli embedding globali e locali differiscono non solo per ciò che rappresentano, ma anche per come vengono generati e utilizzati:

1. **Architettura e Addestramento**:
    
    - **Embedding globali**: Spesso derivati dai token [CLS] o dalla media di tutti i token in modelli come BERT, RoBERTa o modelli addestrati specificamente per catturare la semantica a livello di documento.
    - **Embedding locali**: Potrebbero utilizzare finestre contestuali più piccole o essere addestrati con tecniche di attention focalizzate su segmenti specifici del testo.
2. **Funzioni di Perdita Specializzate**:
    
    - Per gli embedding globali: funzioni che enfatizzano la similarità tematica generale
    - Per gli embedding locali: funzioni che premiano la precisione nel recupero di informazioni specifiche
3. **Dimensionalità e Compressione**:
    
    - Gli embedding globali possono avere dimensionalità maggiore per catturare più sfumature tematiche
    - Gli embedding locali possono essere ottimizzati per efficienza e precisione su dettagli specifici

### Implementazione Pratica

In un sistema HDR completo, il processo potrebbe funzionare così:

1. **Indicizzazione Multi-livello**:
    
    - Creazione di un indice primario con embedding globali di documenti interi
    - Creazione di indici secondari con embedding locali di paragrafi o frasi
    - Collegamento tra gli indici per consentire la navigazione tra livelli
2. **Retrieval Sequenziale**:
    
    - Prima fase: match della query contro embedding globali per identificare i documenti rilevanti
    - Seconda fase: all'interno dei documenti selezionati, match contro embedding locali per estrarre i passaggi più pertinenti
    - Opzionalmente, ulteriori fasi per arrivare a livelli di granularità ancora maggiori
3. **Ponderazione Adattiva**:
    
    - Algoritmi che determinano dinamicamente quanto peso dare agli embedding globali vs. locali in base alla natura della query
    - Per query generali: maggior peso agli embedding globali
    - Per query specifiche: maggior peso agli embedding locali

## Vantaggi del Hierarchical RAG

1. **Miglioramento della Precisione**: Affinando progressivamente la ricerca, riduce il rumore e aumenta la pertinenza dei risultati
    
2. **Gestione di Corpora Estesi**: Scala efficacemente anche con basi di conoscenza molto ampie grazie all'approccio multi-fase
    
3. **Risposte Contestualizzate**: Fornisce non solo informazioni puntuali, ma anche il contesto più ampio in cui si collocano
    
4. **Tracciabilità Migliorata**: La struttura gerarchica rende più facile identificare la provenienza delle informazioni
    
5. **Efficienza Computazionale**: Riduce lo spazio di ricerca ad ogni livello, ottimizzando le risorse
    

## Sfide e Considerazioni

### Sfide Tecniche

1. **Complessità Implementativa**: Richiede sistemi più articolati rispetto al RAG tradizionale
    
2. **Overhead Computazionale**: Potenzialmente più lento a causa dei passaggi multipli
    
3. **Propagazione degli Errori**: Errori nei livelli superiori possono compromettere i livelli inferiori
    

### Considerazioni Architetturali

1. **Bilanciamento Profondità-Ampiezza**: Decidere quanti livelli implementare e quanto ampio deve essere ogni livello
    
2. **Integrazione con Query Rewriting**: Adattare le query a ciascun livello di retrieval
    
3. **Strategie di Indexing**: Ottimizzare gli indici per supportare retrieval a livelli diversi
    

## Applicazioni Avanzate

### Knowledge Graph Integration

Combinare il Hierarchical RAG con grafi di conoscenza:

- I nodi di alto livello rappresentano concetti ampi
- I nodi di basso livello rappresentano fatti specifici
- Le relazioni tra nodi guidano il processo di retrieval gerarchico

### Multi-Modal Hierarchical RAG

Estendere l'approccio gerarchico a contenuti multimodali:

- Primo livello: Identificare documenti pertinenti basati su testo e immagini
- Secondo livello: Analizzare sezioni specifiche di testo e regioni di immagini
- Terzo livello: Estrarre informazioni dettagliate da testo e elementi visivi

### Domain-Specific Hierarchical RAG

Personalizzare la gerarchia in base al dominio:

- Medicina: Dalla specialità medica al sistema corporeo fino a condizioni specifiche
- Legale: Dal codice alle sezioni fino a clausole specifiche
- Educativo: Dal corso alle unità fino ai concetti individuali

## Stato dell'Arte e Sviluppi Futuri

Il Hierarchical RAG rappresenta una frontiera attiva della ricerca, con innovazioni come:

1. **Adaptive Hierarchies**: Strutture gerarchiche che si adattano dinamicamente in base alla query
    
2. **Self-Organizing Knowledge Bases**: Basi di conoscenza che si strutturano automaticamente in gerarchie significative
    
3. **Hybrid Retrieval Strategies**: Combinazione di tecniche diverse (dense retrieval, sparse retrieval, knowledge graph navigation) a diversi livelli della gerarchia
    
4. **Feedback Loops**: Meccanismi che permettono di raffinare i livelli superiori basandosi sui risultati dei livelli inferiori
    

L'evoluzione del Hierarchical RAG promette di migliorare ulteriormente la capacità dei sistemi di intelligenza artificiale di accedere, contestualizzare e utilizzare informazioni complesse in modo efficace e preciso, rappresentando uno dei percorsi più promettenti per superare le limitazioni attuali dei sistemi RAG tradizionali.

NOTE
Quanto di ciò è llm powered? In una RAG standard solo la generation è llm power e, nel caso di ricerca ibrida, la scelta delle key word è llm based. Di norma il retrival fa schifo a causa degli embedder (motivo per cui nasce gte per tagliare un decoder llm e trasformarlo in un embedder, mteb leaderboard hugging face è la pagina con la classifica dello stato dell'arte degli embedding) ed è per questo che si adottano soluzioni come agentic rag.
Come **costruire la gerarchia** è la parte chiave, gemini è molto bravo a farlo, azure parser fa questo senza essere llm powered ma usa modelli layout understanding, vision transformers, cnn, e modelli di ocr. 
Alternativamente puoi descrivere l'albero di gerarchia ad un llm e istruirlo nel parsare

Ma già robe come l'azure parser o gemini di Dani non fanno una sorta di Hierarchical Parsing? Si perché visitando tutte le foglie dell'albero si ottiene l'intero documento originale.


# Hierarchical RAG Zano 

![[Pasted image 20250418095946.png]]

Nota che alcune informazioni potrebbero non essere chiare perché non sono in Allianz e quindi non conosco certe cose date per scontate (pre-processing comune e struttura dei documenti)

## Formato dei File

- I file vengono passati direttamente in markdown utilizzando `markItDown` (cercare repository di Allianz)
- Nello specifico, la funzione `build_hierarchy` riceve solo file markdown

## Creazione della Gerarchia

### Struttura dei Testi

- **Subsection text**: rappresenta tutto il contenuto da un certo livello di heading in giù
    
    - Per CDA (Condizioni di Assicurazione): da `###` in giù
    - Per NT (Nota Tecnica): da `##` in giù
    - Contiene il testo dal livello specificato fino ai prossimi heading dello stesso livello (non compresi)
- **Complete text**: contiene il subsection text più i livelli superiori
    
    - Per NT: include i contenuti di livello `#`
    - Per CDA: include i contenuti di livello `#` e `##`
    - Non include eventuali altri subsection text che stanno in mezzo

### Processo di Embedding

- Si embedda solamente il path (tutti i titoli) + subsection (NON complete_text)
- Si retrieva poi il complete text
- Le tabelle vengono parsate per l'embedding (nel subsection_text), ma nel complete text si mantengono le tabelle originali
    - Motivazione: il complete text non viene embeddato ed embeddare sigle o numeri "casuali" (come generalmente il contenuto delle tabelle) genera rumore
    
    ![[Pasted Graphic.png]]

### Gestione dei Livelli

- I livelli sono definiti dalle intestazioni markdown (`#`, `##`, `###`, ecc.)
- `path_buffer`: gestisce i vari livelli di titoli (es. `#titolo1`, `##titolo1.1`)
    - Rimuove il livello corrispondente quando si cambia sottolivello

## Componenti (hardcoded e non) del Sistema

- La parte di `build_hierarchy` è "hardcodata" specificamente per documenti di tipo Allianz (soprattutto per quanto riguarda i livelli)
- La parte di BM25 e token & lemming è standard e non dipende da Allianz in particolare
    - È riutilizzabile praticamente in toto

## Output

- Il dizionario `hierarchy` finale ritorna quanto specificato nella docstring della funzione