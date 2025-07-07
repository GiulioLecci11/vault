#AI 
[[RAG Concepts]]
[[Hackapizza]]

Fonti: https://medium.com/@zilliz_learn/graphrag-explained-enhancing-rag-with-knowledge-graphs-3312065f99e1 (qui trovi anche alcuni tutorial su come crearla) ma anche https://qdrant.tech/documentation/examples/graphrag-qdrant-neo4j/ (spiegato meglio +altro tutorial, con qdrant e neo4j)

## Introduzione al RAG e GraphRAG

- Il **RAG (Retrieval-Augmented Generation)** combina recupero e AI generativa per migliorare gli LLM con informazioni rilevanti e aggiornate da una knowledge base
    - Integra un database vettoriale e un LLM
    - Il database vettoriale memorizza e recupera informazioni contestuali per le query utente
    - L'LLM genera risposte basate sul contesto recuperato
- Il **GraphRAG** è un metodo innovativo che arricchisce RAG con grafi di conoscenza
    - I grafi di conoscenza memorizzano e collegano dati in base alle loro relazioni semantiche
    - Consente connessioni tra informazioni disparate
    - Fornisce una comprensione più profonda del contesto, portando a risposte più accurate e rilevanti
    - Permette di seguire catene di relazioni per rispondere a query complesse

## Limitazioni e Sfide del RAG Base

- Efficace per query semplici ma limitato per ragionamenti complessi
- Esempio problematico: "_Quale nome fu dato al figlio dell'uomo che sconfisse l'usurpatore Allectus?_"
    - Richiede passaggi multipli: identificare chi sconfisse Allectus, trovare informazioni sul figlio, determinarne il nome
    - Il RAG base recupera testo per similarità semantica, non risponde direttamente a query complesse
- **Sfide principali**:
    1. **Comprensione del contesto:** I modelli possono interpretare erroneamente le query quando il contesto è complesso o ambiguo
    2. **Bilanciamento similarità vs rilevanza:** I sistemi RAG faticano a garantire che le informazioni recuperate siano sia simili che contestualmente rilevanti
    3. **Completezza delle risposte:** I RAG tradizionali potrebbero non catturare tutti i dettagli rilevanti per query che richiedono agli LLM di trovare relazioni non esplicitamente presenti nel contesto
- **Limitazioni del top-k retrieval:** Il recupero top-k nei sistemi RAG tradizionali raramente funziona in modo ottimale
    - Esempio: per riassumere una biografia dove ogni capitolo descrive un traguardo specifico di un individuo, il RAG tradizionale recupera solo i primi k chunk mentre avrebbe bisogno dell'intero contesto
    - GraphRAG risolve questo problema:
        - Costruendo un grafo con entità e relazioni dai documenti
        - Attraversando il grafo per il recupero contestuale
        - Inviando l'intero contesto pertinente all'LLM per una risposta completa

## Come GraphRAG Migliora il Recupero del Contesto

- Un sistema (tipicamente un LLM) crea un grafo dai documenti
- Per l'esempio della biografia, viene creato un sottografo per la persona (P) dove ogni traguardo è a un hop di distanza dal nodo entità di P
- Durante la sintesi, il sistema può eseguire un attraversamento del grafo per recuperare tutto il contesto rilevante legato ai traguardi di P
- L'intero contesto aiuta l'LLM a produrre una risposta completa, mentre il RAG ingenuo non ci riuscirebbe
- I sistemi GraphRAG sono anche migliori dei sistemi RAG tradizionali perché gli LLM sono intrinsecamente abili nel ragionamento con dati strutturati

## Processo di Indicizzazione in GraphRAG

1. **Segmentazione del Testo**
    - Divisione del corpus in unità testuali (chunk) più piccole
    - Possono essere paragrafi, frasi o altre unità logiche
    - Preserva informazioni dettagliate dai documenti lunghi
2. **Estrazione di Entità, Relazioni e Affermazioni**
    - Uso di LLM per identificare entità (persone, luoghi, organizzazioni)
    - Rilevamento delle relazioni tra entità (formano i nodi e gli archi del grafo)
    - Estrazione di affermazioni chiave dal testo
    - Costruzione del grafo di conoscenza iniziale
3. **Clustering Gerarchico**
    - Applicazione della tecnica Leiden per rilevare strutture comunitarie nel grafo
    - Le entità vengono assegnate a diverse comunità per un'analisi più approfondita
    - Algoritmo bottom-up che organizza il grafo in gruppi semantici gerarchici
    - Una comunità è un gruppo di nodi densamente connessi tra loro ma scarsamente collegati ad altri gruppi
    - Consente la comprensione a diversi livelli di astrazione
4. **Generazione di Riassunti Comunitari**
    - Creazione di sommari per ogni comunità con approccio bottom-up
    - I riassunti includono entità principali, relazioni e affermazioni chiave
    - Fornisce una panoramica dell'intero dataset e contesto per le query successive

## Processi di Interrogazione in GraphRAG

### Ricerca Globale

- Per il ragionamento su domande olistiche relative all'intero corpus
- Fasi del flusso di lavoro:
    1. **Input iniziale:** acquisizione query utente e cronologia conversazione
    2. **Report comunitari in batch:** utilizzo di report generati dall'LLM dal livello specificato della gerarchia comunitaria
        - I report vengono mescolati e divisi in più batch
    3. **Risposte Intermedie Valutate (RIR):** ogni batch genera risposte intermedie
        - Ogni risposta contiene punti informativi con punteggi di importanza
    4. **Classificazione e Filtraggio:** selezione dei punti più importanti dalle risposte intermedie
        - Formazione delle Risposte Intermedie Aggregate
    5. **Risposta Finale:** generazione della risposta finale utilizzando le risposte intermedie aggregate come contesto

### Ricerca Locale

- Consigliata per domande su entità specifiche (persone, luoghi, organizzazioni)
- Passi del processo:
    1. **Query utente:** ricezione della domanda
    2. **Ricerca di entità simili:** identificazione di entità semanticamente correlate all'input utente
        - Queste entità fungono da punti di ingresso nel grafo
        - Utilizzo di database vettoriali come Milvus per ricerche di similarità testuale
    3. **Mappatura entità-unità testuali:** collegamento tra entità estratte e unità di testo
    4. **Estrazione relazioni tra entità:** recupero di informazioni specifiche sulle entità e le loro relazioni
    5. **Mappatura entità-covarianti:** collegamento delle entità ai loro attributi statistici o altri dati rilevanti
    6. **Integrazione report comunitari:** incorporazione di informazioni globali nei risultati di ricerca
    7. **Utilizzo della cronologia conversazione:** considerazione del contesto precedente per comprendere l'intento dell'utente
    8. **Generazione della risposta:** costruzione della risposta finale basata sui dati filtrati e ordinati

## Architettura del GraphRAG

L'architettura ha due componenti principali:

### 1. Fase di Ingestion (Indicizzazione)

Combina un **Database a Grafo** e un **Database Vettoriale** per migliorare i flussi di lavoro RAG:

1. **Dati Grezzi:** Servono come fondamento, comprendendo contenuti strutturati o non strutturati
2. **Creazione dell'Ontologia:** Un **LLM** elabora i dati grezzi in un'**ontologia**, strutturando entità, relazioni e gerarchie
    - Esistono approcci alternativi per estrarre informazioni più strutturate dai dati grezzi, come l'uso del NER (Named Entity Recognition)
3. **Database a Grafo:** L'ontologia viene memorizzata in un **database a grafo** (es. Neo4j) per catturare relazioni complesse
4. **Embedding Vettoriali:** Un **modello di embedding** converte i dati grezzi in vettori ad alta dimensionalità che catturano similarità semantiche
5. **Database Vettoriale:** Questi embedding vengono memorizzati in un **database vettoriale** (es. Qdrant) per il recupero basato sulla similarità
6. **Interconnessione dei Database:** I database a grafo e vettoriale condividono ID unici, consentendo il riferimento incrociato tra risultati basati su ontologia e vettori

### 2. Fase di Retrieval & Generation (Recupero e Generazione)

Progettata per gestire le query degli utenti sfruttando sia la ricerca semantica che l'estrazione del contesto basata su grafo:

1. **Vettorizzazione della Query:** Un modello di embedding converte la query utente in un vettore ad alta dimensionalità
2. **Ricerca Semantica:** Il vettore esegue una ricerca basata sulla similarità nel database vettoriale, recuperando documenti o voci rilevanti
3. **Estrazione ID:** Gli ID estratti dai risultati della ricerca semantica vengono utilizzati per interrogare il database a grafo
4. **Recupero del Contesto dal Grafo:** Il database a grafo fornisce informazioni contestuali, incluse relazioni ed entità
5. **Generazione della Risposta:** Il contesto recuperato dal grafo viene passato a un LLM per generare una risposta finale
6. **Risultati:** La risposta generata viene restituita all'utente

Questa architettura combina i punti di forza di entrambi i database:

- **Ricerca Semantica con Database Vettoriale:** La query viene elaborata semanticamente per identificare i punti dati più rilevanti
- **Espansione Contestuale con Database a Grafo:** Gli ID o le entità recuperati interrogano il database a grafo per relazioni dettagliate
- **Generazione Migliorata:** L'architettura combina rilevanza semantica e contesto basato su grafo per generare risposte più informate e ricche di contesto

## Sfide del GraphRAG

Nonostante i suoi vantaggi, l'approccio GraphRAG centrato su LLM affronta diverse sfide:

1. **Costruzione del Grafo di Conoscenza con LLM:**
    - Rischi di inconsistenze e propagazione di bias o errori
    - Mancanza di controllo sull'ontologia utilizzata
2. **Interrogazione del Grafo con LLM:**
    - Un LLM traduce la query umana in linguaggio Cypher (linguaggio di query di Neo4j)
    - La creazione di query complesse in Cypher può portare a risultati imprecisi
3. **Scalabilità e Considerazioni sui Costi:**
    - Affidarsi agli LLM aumenta i costi e diminuisce la scalabilità
    - Gli LLM vengono utilizzati ogni volta che i dati vengono aggiunti, interrogati o generati

Per affrontare queste sfide, potrebbe essere necessario un sistema di rappresentazione della conoscenza più controllato e strutturato affinché GraphRAG funzioni in modo ottimale su larga scala.

NOTE: Per estrazione di entità e relazioni si usa un LLM con un prompt specifico, tuttavia dei problemi possono nascere quando non si ha una conoscenza ontologica dell'argomento (magari un utente fa una domanda utilizzando un termine che non è presente nei tuoi documenti e quindi non riesci a collegarlo a nulla)

- Ma un graph db devo immaginarlo come il modello er di un db relazionale? Si potrebbe fare ma non è pratico, basta descrivere le entità e come sono collegate tra loro
- Nella parte di querying è tutto affidato da un LLM? Solitamente si per farsi scrivere le query in cypher partendo da linguaggio naturale

La graphrag è una specificazione di una [[Agentic RAG]] perché c'è un agente che riscrive le query in cypher

Global e Local search: Si parla sempre di ricerca semantica, nella global search si da lo stesso peso a tutte le componenti dei vettori di embedding su cui si fa ricerca. Nella local search invece si usano versioni sparse dei vettori concentrandosi solo su componenti che risultano più pesanti, mandando a zero le componenti meno influenti. Vedila come una sorta di PCA ([[Principal Component & Linear Discrimination Analysis]]) dove ti concentri sul differenziare alcune dimensionalità. L'algoritmo di Local Search capisce da solo su quali componenti concentrarsi utilizzando norme specifiche, come versioni avanzate di Ridge Lasso e compagnia.