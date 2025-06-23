I LLM sono basati su reti neurali profonde (Deep neural networks -> tanti layer; Mentre le wide neural networks hanno tanti neuroni per ogni layer), tipicamente architetture Transformer.

Transformers:
Encoder-Decoder: I transformer possono avere una struttura a doppio blocco. L'encoder processa l'input, mentre il decoder genera l'output. Tuttavia, i modelli come BERT usano solo encoder (per comprensione) e GPT usa solo decoder (per generazione).

Meccanismo di attenzione: La self-attention è il cuore del Transformer. Ogni token "presta attenzione" a tutti gli altri token dell'input per catturare relazioni contestuali.
Il tutto consiste nel calcolare le relazioni tra parole (o token) usando matrici di query (Q), key (K) e value (V). Questo permette al modello di focalizzarsi solo sulle parti importanti dell'input.

Tokenizer:
Prima che i dati vengano elaborati, il testo viene spezzato in token:
Token possono essere parole, sottoparole o caratteri.
Tecniche comuni: Byte Pair Encoding (BPE) o WordPiece.

Pretrain and fine tuning:
Pre-training: Consiste nella fase di allenamento del "grande modello" appartenente solitamente ad una grande azienda come OpenAI o Anthropic. Sulla base di questi grandi modelli poi si effettua il fine tuning per adattarli a compiti specifici.
Il modello viene addestrato su enormi dataset (es. Wikipedia, libri, pagine web) con obiettivi come:
Language Modeling (GPT): Prevedere il prossimo token dato un contesto.
Masked Language Modeling (BERT): Predire token mascherati nel testo.
Fine-tuning: Dopo il pre-training, il modello può essere adattato per compiti specifici, come classificazione, traduzione o QA.

Performance e metriche:
Le performance di un LLM dipendono da diversi fattori, come l'architettura, la qualità dei dati e la capacità computazionale.

a. Metriche comuni
Perplexity: Misura quanto bene il modello predice sequenze di testo. Minore è la perplexity, migliore è il modello.
BLEU/ROUGE: Valutano la qualità del testo generato confrontandolo con un riferimento.
Accuracy/F1-score: Usati in compiti classificatori o estrattivi.
Human evaluation: Per valutare coerenza, creatività o correttezza.

b. Scaling Laws
Modelli più grandi (più parametri) e dataset più ampi tendono a performare meglio, seguendo leggi scalari empiriche. Tuttavia, la crescita esponenziale dei parametri comporta diminuzioni marginali dei guadagni rispetto al costo computazionale.
Si ha quindi sempre a che fare con un tradeoff tra dimensioni ed efficienza. Modelli come GPT offrono grandi performance al prezzo di alta potenza computazionale (nei server) richiesta

Utilizzi:
I LLM sono utilizzati in una vasta gamma di applicazioni:

a. NLP (Natural Language Processing)
Chatbot e assistenti virtuali: Come ChatGPT o Alexa.
Traduzione automatica: Google Translate, DeepL.
Content generation: Scrittura creativa, generazione di codice, sintesi di documenti.
Queste applicazioni solitamente sono implementate solitamente tramite transformers o RNN (che soffrono però del problema dei vanishing gradient)
b. Ricerca e analisi
Text mining: Estrazione di informazioni da grandi corpus.
Sistemi di raccomandazione: Basati su analisi semantica del testo.
c. AI nella programmazione
Generazione di codice (es. GitHub Copilot).
Correzione di bug o completamento automatico.
d. Medicina e scienza
Analisi di paper scientifici, riassunti automatizzati.
Diagnosi basate su testi clinici.


Drawbacks:
Nonostante i loro successi, i LLM presentano alcune limitazioni:

Bias: I modelli apprendono dai dati di addestramento, che spesso contengono pregiudizi.
Hallucinations: Tendono a "inventare" informazioni plausibili ma errate.
Costo computazionale: Elevati costi in termini di energia e risorse.
Generalizzazione limitata: Non possono comprendere appieno concetti al di fuori del training.

Advanced Concepts:
1)Few-shot e Zero-shot Learning
Definizione
Few-shot learning: Il modello risolve un compito fornendo pochi esempi di input-output nel prompt, dimostrando la capacità di adattarsi rapidamente a nuovi task.
Zero-shot learning: Il modello risolve un compito senza esempi espliciti, basandosi sulla generalizzazione acquisita durante l’addestramento.

Gli LLM usano il contesto fornito nel prompt per comprendere il compito da svolgere. La capacità deriva dal pre-training su enormi corpus di dati che includono molteplici task impliciti.

Few-shot learning

Task: Tradurre una frase in italiano.
Esempio 1:
Inglese: "I love programming."
Italiano: "Amo programmare."

Esempio 2:
Inglese: "The weather is nice today."
Italiano: "Il tempo è bello oggi."

Traduci:
Inglese: "She is reading a book."
Italiano:

Zero-shot learning

Task: Classificare il sentimento di una frase come positivo o negativo.

Prompt:
Determina se il sentimento espresso nella seguente frase è positivo o negativo:
"I am very happy with the service."


2)Retrieval-Augmented Generation (RAG)
Definizione
RAG è un metodo che combina un modello di linguaggio con un motore di ricerca o un sistema di retrieval per arricchire le risposte con informazioni esterne. Invece di basarsi esclusivamente sulla conoscenza interna del modello, RAG cerca documenti o dati rilevanti e li utilizza per generare un output informato.

Come funziona?
Query: L'utente pone una domanda.

Retrieval: Un sistema esterno recupera documenti o informazioni pertinenti (ad es., da un database o un motore di ricerca).
Strumenti comuni per recuperare le informazioni dalla knowledge base sono:
BM25 (basato su keyword match, usato in Elasticsearch).
Dense Retrieval con modelli come SENTENCE-BERT, che usa embeddings per calcolare la similarità semantica.

Generazione: Il modello di linguaggio utilizza queste informazioni per costruire una risposta.

Esempio
Task: Rispondere alla domanda: "Chi ha vinto il premio Nobel per la pace nel 2023?"
Flusso RAG:
Query: Il modello identifica il contesto della domanda.
Retrieval: Recupera articoli aggiornati sul premio Nobel per la pace.
Output: "Il premio Nobel per la pace nel 2023 è stato assegnato a XYZ."
Questo approccio garantisce che il modello fornisca risposte aggiornate anche se il training è avvenuto PRIMA del 2023.
Vantaggi
Riduce il rischio di hallucinations (risposte inventate).
Permette di rispondere a domande su argomenti non presenti nel training.
Migliore accuratezza: Usa dati esterni per evitare risposte inventate.
Adattabilità: Può essere personalizzato per domini specifici (es., medicina, finanza).
Scalabilità: Può gestire grandi quantità di documenti.

3) Prompt Engineering
Definizione
Il prompt engineering consiste nell'arte di progettare input testuali per guidare un modello verso una risposta desiderata. È particolarmente cruciale per ottenere prestazioni ottimali da LLM.

Tecniche comuni
Istruzioni chiare: Dare al modello istruzioni esplicite.
Esempi contestuali: Includere input-output per fornire un contesto.
Formato strutturato: Usare layout precisi per ridurre ambiguità.


NLP (Natural Language Processing):
Come detto, è il sottoinsieme dell'AI che si occupa di comprendere il linguaggio umano scritto o parlato. Esso si basa su dei fondamentali come:
Tokenizzazione, Stop Word Removal (rimuovere parole comuni che non cambiano il senso della frase come "e", "il", "che" con librerie come NLTK, spaCy), Stemming (Riduce le parole alla loro radice, ma può generare parole non esistenti) Lemmatization (restituisce la forma base di una parola (lemma), più precisa rispetto allo stemming),Part-of-Speech (POS) Tagging( Assegna a ogni parola il suo ruolo grammaticale (sostantivo, verbo, ecc.
Utile per capire la struttura di una frase.),
Named Entity Recognition( (NER) Identifica entità specifiche come nomi propri, luoghi, date. Per esempio "Apple" può essere riconosciuto come una compagnia e non come un frutto.), Parsing(Analizza la struttura sintattica delle frasi),Word Embeddings(Rappresentano parole in uno spazio vettoriale continuo, catturandone significati semantici e relazioni (librerie Word2Vec, GloVe))

Transfer Learning:
Il Transfer Learning è una tecnica in cui un modello pre-addestrato su un compito viene riutilizzato (o adattato) per un altro compito correlato. È particolarmente utile quando i dati per il nuovo compito sono limitati (e si opta quindi per un fine-tuning cioè quando un modello pre-addestrato viene ulteriormente addestrato su un dataset specifico per il nuovo compito e solo alcune parti del modello vengono aggiornate (ad esempio, l'ultimo layer)).

Distillation:
Tecnica di transfer learning dove si utilizza un modello <teacher> per allenarne uno <student> solitamente più piccolo e veloce da utilizzare ed allenare. Il training consiste nell'utilizzare l'output del modello teacher come truth model per lo student.

Esempi di Transfer Learning
NLP:
Modelli come BERT, GPT e T5 vengono pre-addestrati su corpus generici e poi fine-tuned su compiti specifici come classificazione del sentiment, NER, ecc.
Esempio: Utilizzare BERT per classificare recensioni Amazon in "Positive" o "Negative".

Visione artificiale:
Modelli come ResNet, VGG e EfficientNet pre-addestrati su ImageNet vengono usati per la classificazione di immagini personalizzate.


4) LSTM: Tipo speciale di Recurrent Neural Networks (RNN) progettato per catturare dipendenze a lungo termine nei dati sequenziali, sono particolarmente utili per analizzare sequenze di testo, poiché permettono di identificare le relazioni tra parole anche se distanti nel tempo o nella sequenza.

Memoria e cancellazione selettiva: LSTM utilizza tre principali "porte" (gate) per controllare il flusso di informazioni:
Input gate: Decide quali nuove informazioni aggiungere alla memoria.
Forget gate: Determina quali informazioni vecchie devono essere dimenticate.
Output gate: Decide quali informazioni della memoria devono essere utilizzate per l'output.
Grazie alla gestione della memoria, le LSTM possono catturare pattern più complessi rispetto a una RNN semplice, che tende a dimenticare il contesto con sequenze lunghe.

5) MAXPOOL: Il livello GlobalMaxPool1D viene utilizzato per ridurre dimensionalità, prendendo il massimo valore per ogni filtro lungo tutta la sequenza temporale (lunghezza della sequenza). Questo livello scorre l'output sequenziale dell'LSTM e seleziona il valore massimo per ogni feature.
Riducendo i dati in un unico vettore, GlobalMaxPool1D diminuisce il numero di parametri e facilita il passaggio dei dati ai livelli densi successivi.

Perché usarli insieme?
LSTM è potente per catturare il contesto sequenziale, ma produce un output voluminoso (un vettore per ogni passo temporale).
GlobalMaxPool1D riduce l'output dell'LSTM in un vettore più gestibile, eliminando informazioni ridondanti e consentendo al modello di concentrarsi sulle feature più significative.


Foundation Models:
Si riferisce a modelli generalmente di deep networks, allenati su grandissime quantità di dati non supervisionati e che può essere adattato a una vasta gamma di compiti tramite fine-tuning (o anche tramite transfer learning). Questi sono solitamente dei modelli molto complessi e vengono addestrati su dati molto generici per poter performare a dovere un po' su tutto (efficacia cross-domain)

Transfer learning:
Abilità di un modello di performare bene su dati sui quali non è mai stato allenato, i "transferable models" riducono quindi la necessità di creare dataset e modelli task-specific
#AI 