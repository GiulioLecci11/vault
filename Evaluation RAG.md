#AI  
[[RAG Concepts]]
[[Advanced RAG]]

I sistemi di Retrieval-Augmented Generation (RAG) rappresentano un'evoluzione significativa nel campo dell'elaborazione del linguaggio naturale. Questi sistemi combinano due componenti principali: un retriever che estrae informazioni rilevanti da fonti esterne, e un generator che produce risposte coerenti utilizzando queste informazioni. La loro architettura ibrida consente di migliorare significativamente l'affidabilità delle risposte generate, riducendo il fenomeno delle allucinazioni (informazioni plausibili ma non veritiere) tipico dei modelli linguistici generativi autonomi.

La valutazione di questi sistemi è particolarmente complessa per diversi motivi:

1. **Struttura ibrida**: I sistemi RAG combinano retrieval e generation, rendendo necessaria una valutazione che consideri entrambi i componenti e la loro interazione.
    
2. **Fonti di conoscenza dinamiche**: I sistemi RAG dipendono da database esterni che possono cambiare nel tempo, aggiungendo una dimensione temporale alla valutazione.
    
3. **Diversità di applicazioni**: I sistemi RAG sono utilizzati per vari compiti (risposta a domande, generazione di contenuti, ecc.), ognuno con requisiti di valutazione specifici.
    

Come evidenziato nella letteratura scientifica, il framework "**A Unified Evaluation Process of RAG**" (Auepora) propone un approccio sistematico per la valutazione dei sistemi RAG, concentrandosi su tre domande fondamentali:

- Cosa valutare? (Target)
- Come valutare? (Dataset)
- Come misurare? (Metriche)

## Ground Truth nel contesto RAG

Il concetto di "ground truth" è fondamentale nell'evaluation dei sistemi RAG. Si riferisce alle risposte o documenti corretti che un sistema ideale dovrebbe produrre o recuperare. Questa "verità di riferimento" serve come standard per misurare le prestazioni del sistema.

Nel contesto RAG, possiamo identificare diverse forme di ground truth:

1. **Query**: La richiesta originale dell'utente.
2. **Documenti candidati**: L'insieme di documenti potenzialmente rilevanti.
3. **Documenti rilevanti**: I documenti effettivamente pertinenti alla query.
4. **Risposte di esempio**: Risposte corrette pre-definite per una query.
5. **Output strutturati**: Format specifici di output attesi dal sistema.

La sfida principale consiste nel definire cosa costituisce una "verità di riferimento" in contesti dove le risposte possono essere soggettive o multiple risposte possono essere considerate corrette. Questo è particolarmente problematico in applicazioni come la generazione creativa o risposta a domande aperte.

## Golden Dataset e Golden Retrieval

Nel contesto dell'evaluation dei sistemi RAG, il termine "golden" si riferisce tipicamente a standard di riferimento di alta qualità. I due concetti principali sono:

1. **Golden Dataset**: Un insieme di dati curato manualmente che contiene query, documenti rilevanti e risposte ideali. Questi dataset sono considerati lo standard gold per la valutazione dei sistemi RAG poiché sono stati verificati da esperti umani. La creazione di questi dataset è un processo laborioso che richiede notevoli risorse, ma garantisce un'elevata qualità nella valutazione.
    
2. **Golden Retrieval**: Si riferisce ai documenti ideali che dovrebbero essere recuperati per una determinata query. La valutazione di un sistema RAG spesso confronta i documenti recuperati con questi "golden retrieval" per misurare l'accuratezza del componente di retrieval. In pratica, determinare quali documenti costituiscono il "golden retrieval" può essere soggettivo e dipendere dal contesto specifico dell'applicazione.
    

Dataset come WikiEval, Natural Questions (NQ), HotpotQA e FEVER sono esempi di dataset comunemente utilizzati per la valutazione dei sistemi RAG. Questi dataset forniscono un ampio spettro di query, con corrispettivi documenti rilevanti e risposte attese, consentendo una valutazione standardizzata delle prestazioni dei sistemi RAG.

## Framework di Valutazione per Sistemi RAG

Esistono diversi framework di valutazione sviluppati specificamente per i sistemi RAG:

1. **RAGAS**: Un framework di valutazione automatizzata che si concentra sulla rilevanza del contesto e la fedeltà delle risposte. RAGAS valuta la capacità del sistema di recuperare documenti pertinenti e di generare risposte che rimangano fedeli a tali documenti.
    
2. **ARES**: Un framework che utilizza LLM assistiti da classificatori per valutare diversi aspetti delle risposte generate, inclusi rilevanza, fedeltà e correttezza.
    
3. **RGB**: Un benchmark che valuta l'integrazione delle informazioni, la robustezza al rumore e il rifiuto negativo (la capacità di non fornire risposte quando appropriato).
    
4. **ReEval**: Un framework specifico per la valutazione delle allucinazioni nei sistemi RAG, focalizzato sulla capacità del sistema di evitare informazioni non supportate dai documenti recuperati.
    

Un approccio emergente è l'uso di "LLM as a Judge", dove modelli linguistici di grandi dimensioni sono utilizzati per valutare le risposte generate. Questo metodo si avvicina al giudizio umano ma offre maggiore scalabilità, consentendo la valutazione automatica di grandi volumi di risposte.

## Metriche di Valutazione in RAG

Le metriche di valutazione per i sistemi RAG possono essere categorizzate in base ai target di valutazione:

### Metriche per il Retrieval

**Non basate sul ranking**:

- **Accuracy**: Proporzione di risultati corretti sul totale.
- **Precision**: Frazione di documenti rilevanti tra quelli recuperati.
- **Recall**: Frazione di documenti rilevanti recuperati sul totale dei documenti rilevanti.

**Basate sul ranking**:

- **MRR (Mean Reciprocal Rank)**: Media del reciproco della posizione della prima risposta corretta.
- **MAP (Mean Average Precision)**: Media delle precision medie per ciascuna query.
- **Log Rank Index**: Misura la posizione di un chunk corretto all'interno della classifica di risultati rispetto alla sua posizione nella classifica corretta (golden truth). Questa metrica è particolarmente utile per valutare quanto accuratamente il sistema di retrieval ordina i documenti rilevanti.

### Metriche per la Generation

- **ROUGE (Recall-Oriented Understudy for Gisting Evaluation)**: Un insieme di metriche progettate per valutare la qualità dei riassunti confrontandoli con riassunti di riferimento generati da umani. ROUGE misura la sovrapposizione tra il testo generato e il testo di riferimento, utilizzando diverse varianti:
    
    - **ROUGE-N**: Misura la sovrapposizione di n-grammi (sequenze continue di n parole) tra il testo generato e il riferimento.
    - **ROUGE-L**: Valuta la più lunga sottosequenza comune tra il testo generato e il riferimento, tenendo conto dell'ordine delle parole.
    - **ROUGE-W**: Simile a ROUGE-L ma assegna pesi maggiori a sottosequenze consecutive.
    - **ROUGE-S**: Considera skip-bigrams (coppie di parole in qualsiasi ordine con parole intermedie) per valutare la similarità.
    
    ROUGE è particolarmente utile per valutare quanto bene il sistema RAG cattura le informazioni chiave presenti nei documenti di riferimento, ma ha limitazioni nel valutare la coerenza semantica e la fluidità del testo.
    
- **BLEU (Bilingual Evaluation Understudy)**: Originariamente sviluppato per la traduzione automatica, BLEU calcola la precisione degli n-grammi nel testo generato rispetto a uno o più testi di riferimento, applicando anche una penalità per testi troppo brevi. Il punteggio BLEU è calcolato come:
    
    1. Conteggio degli n-grammi nel testo generato che appaiono anche nel riferimento.
    2. Divisione di questo conteggio per il numero totale di n-grammi nel testo generato.
    3. Applicazione di una penalità di brevità per scoraggiare risposte troppo corte.
    
    BLEU è spesso criticato per la sua incapacità di catturare la rilevanza semantica e la fluidità grammaticale, poiché si basa esclusivamente sul confronto di n-grammi. Tuttavia, rimane una metrica standard per la sua semplicità e correlazione con il giudizio umano in molti casi.
    
- **BERTScore**: Utilizza embedding contestuali da modelli pre-addestrati come BERT per valutare la similarità semantica tra testo generato e riferimento. BERTScore supera alcune limitazioni di ROUGE e BLEU considerando il significato delle parole nel contesto.
    
- **LLM as a Judge**: Usa LLM per valutare coerenza, rilevanza e fluidità delle risposte generate, fornendo valutazioni più nuanciate e simili al giudizio umano.
    

### Requisiti Aggiuntivi

Oltre alle metriche di base per retrieval e generation, i sistemi RAG vengono valutati su requisiti aggiuntivi:

- **Latenza**: Tempo di risposta del sistema, cruciale per l'esperienza utente.
- **Diversità**: Varietà nelle informazioni recuperate e nelle risposte generate.
- **Robustezza al rumore**: Capacità di gestire informazioni irrilevanti senza compromettere la qualità della risposta.
- **Rifiuto negativo**: Capacità di astenersi dal fornire risposte quando le informazioni disponibili sono insufficienti.
- **Robustezza controfattuale**: Capacità di identificare e ignorare informazioni errate, anche quando segnalate come potenzialmente ingannevoli.

## Approcci avanzati all'Evaluation RAG

### Evaluation distinta per Retrieval e Generation

L'evaluation dei sistemi RAG è spesso suddivisa in due componenti distinte, rispecchiando l'architettura del sistema stesso:

1. **Evaluation del Retrieval**: Focalizzata sulla capacità del sistema di recuperare documenti rilevanti.
    
    - Il **Log Rank Index** è una metrica particolarmente utile che valuta la posizione di un chunk corretto all'interno della classifica di risultati rispetto alla sua posizione nella classifica corretta (golden truth). Questa metrica aiuta a determinare quanto accuratamente il sistema ordina i documenti per rilevanza.
    
2. **Evaluation della Generation**: Concentrata sulla qualità, correttezza e completezza delle risposte generate.
    
    - Presso Allianz, l'evaluation di generation viene curata da un'esperta (Elisa) che valuta manualmente la qualità delle risposte generate.
    - È stato sviluppato un approccio innovativo basato su algoritmi genetici che cerca di replicare il giudizio umano esperto. In particolare, "Zano" ha creato un algoritmo genetico che opera attraverso la ricerca di minimi locali per generare prompt che producano risultati il più possibile simili a quelli valutati dall'esperta umana in termini di correttezza e completezza.
    - Questo approccio rappresenta essenzialmente una forma di prompt tuning automatizzato, anche se presenta limitazioni in termini di riutilizzabilità in contesti diversi.

### Ottimizzazione dei prompt per l'evaluation

L'ottimizzazione dei prompt per l'evaluation rappresenta un'area di ricerca emergente. L'approccio basato su algoritmi genetici menzionato sopra è un esempio di come si possa tentare di automatizzare il processo di creazione di prompt che producano valutazioni allineate con il giudizio umano.

Questo metodo:

- Utilizza tecniche di ottimizzazione per trovare configurazioni di prompt che massimizzino la correlazione con le valutazioni umane
- Si concentra su metriche specifiche come correttezza e completezza
- Rappresenta un tentativo di bridging tra valutazione automatica e giudizio umano
- Ha limitazioni in termini di generalizzabilità e trasferibilità a nuovi domini o contesti

## Sfide e Direzioni Future

La valutazione dei sistemi RAG presenta diverse sfide persistenti:

1. **Soggettività**: La definizione di "risposta corretta" può variare in base al contesto e all'utente.
2. **Dinamicità dei database**: L'evoluzione delle fonti di conoscenza nel tempo complica la valutazione a lungo termine.
3. **Compromesso tra precisione e latenza**: Trovare il giusto equilibrio tra qualità delle risposte e tempo di risposta.
4. **Allineamento con le preferenze umane**: Garantire che le metriche riflettano ciò che gli utenti considerano importante.
5. **Automatizzazione dell'evaluation**: Sviluppare metodi che possano scalare senza perdere qualità rispetto alla valutazione umana, come nel caso dell'approccio basato su algoritmi genetici descritto precedentemente.

Le direzioni future includono lo sviluppo di benchmark più comprensivi che coprano diversi domini e applicazioni, metriche più sofisticate che catturino meglio le sfumature della comprensione del linguaggio, e metodi di valutazione più efficienti dal punto di vista computazionale, particolarmente importante per l'uso su larga scala.

La continua evoluzione delle tecniche di valutazione è essenziale per guidare il miglioramento dei sistemi RAG, facilitando lo sviluppo di soluzioni più accurate, affidabili e utili per gli utenti finali.