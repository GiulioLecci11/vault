Per capire il Transformer, partiamo analizzando il problema che tenta di risolvere.

Il Transformer è diventato popolare sulla scia del successo dell'architettura _seq2seq_. Quando questa è stata introdotta, nel 2014, ha portato a significativi miglioramenti in due task che al tempo erano molto difficoltosi: traduzione e riassunto. Nel 2016, Google ha incorporato la seq2seq in Google Translate, dichiarando che ciò aveva portato al più grande miglioramento di sempre nella qualità della traduzione nel loro prodotto.

Ad alto livello, seq2seq consiste di un _encoder_ che processa l'input e di un _decoder_ che genera l'output. Sia l'input che l'output sono _sequenze_ di token (da cui il nome dell'architettura). Seq2seq usa le recurrent neural networks (RNNs) per i due componenti di encoder e decoder.

Nella sua forma basica, l'encoder processa i token di input in modo sequenziale, e produce un **hidden state** finale che rappresenta l'input. Il decoder poi produce i token di output in modo sequenziale, usando questo hidden state e i token che esso stessa genera via via.

Questa architettura presenta due problemi principali:

1. usando solo l'hidden state che rappresenta l'input (e non le singole parole dell'input), è come se qualcuno rispondesse a delle domande su un libro avendo letto solo il riassunto del libro. Questo limita la qualità finale dell'output
2. lavorando in modo sequenziale, il processamento è molto lento per sequenze lunghe

Il Transformer affronta entrambi i problemi usando l'attention mechanism. Questo meccanismo permette al modello di pesare l'importanza di differenti token di input quando produce i token di output. Tornando all'esempio di prima, è come se adesso rispondessimo alle domande su un libro potendo accedere a qualsiasi pagina del libro stesso.

L'architettura Transformer fa del tutto a meno delle RNNs.

Con questa architettura, i token di input possono essere processati in parallelo, velocizzando significativamente il processo nella fase iniziale. Pur rimuovendo il processamento sequenziale dell'input, nei modelli di linguaggio autoregressivi basati su Transformer rimane il collo di bottiglia dell'output, che viene ancora prodotto un token alla volta.

L'inferenza quindi consiste di due step:

1. Prefill: il modello processa i token di input in parallelo, creando gli stati intermedi che sono necessari per generare il primo token di output
2. Decode: il modello genera i token di output uno alla volta

### Attention Mechanism

Come detto, al centro dell'architettura Transformer risiede l'attention mechanism.

Al suo interno, questo meccanismo si basa su tre vettori:

- **Query Vector** (Q) che rappresenta lo stato corrente del decoder ad ogni decoding step. Usando l'esempio del libro, questo vettore può essere immaginato come la persona che sta cercando informazioni per creare un riassunto del libro
- Ogni **Key Vector** (K) rappresenta un token precedente. Se ogni token precedente è una pagina del libro, ogni key vector è la pagina del libro. Ad ogni decoding step, i "token precedenti" includono sia i token di input sia i token di output finora generati
- Ogni **Value Vector** (V) rappresenta il valore effettivo di un token precedente. Ogni Value Vector è come se fosse il contenuto di una pagina del libro

Il meccanismo di attenzione calcola quanta attenzione dare ad un token di input calcolando il _dot product_ tra il query vector e il key vector di quel token. Un valore alto significa che il modello userà maggiormente il "contenuto della pagina" corrispondente a quel token (ovvero il value vector) nel processo di generazione. In altri termini, l'output sarà maggiormente influenzato dal contenuto informativo che quel token si porta dietro.

![[Pasted image 20250903113827.png]]

Data una sequenza di input, i valori di questi 3 vettori vengono calcolati usando tre matrici omonime.

Il meccanismo di attenzione è (quasi) sempre _multi-headed_. Questo vuol dire che, invece di valutare l'attenzione solo da una _prospettiva_, il modello fa più confronti parallelamente da "angolazioni" diverse, usando più "teste". Ogni testa è una versione più piccola dell'attention mechanism che si concentra su un aspetto specifico. Questo permette di catturare più tipi di pattern tra le parole (es. grammatica, semantica, struttura, dipendenze a lungo raggio, dipendenze a corto raggio, ...).

Gli output di tutte le attention heads vengono poi concatenati e, dopo un'opportuna trasformazione, vengono dati in pasto allo step computazionale successivo.

### Transformer block

Un'architettura Transformer è composta da più blocchi transformer. In generale, ogni blocco transformer contiene due moduli:

1. Attention module: ognuno di essi è costituito da quattro matrici dei pesi - query, key, value e output projection
2. MLP module: consiste da una sequenza di layers lineari (chiamati anche feedforward layers) divisi da una funzione di attivazione non lineare. Ogni linear layer è una matrice di pesi usata per applicare trasformazioni lineari, mentre la funzione di attivazione permette ai linear layers di imparare anche dei pattern non lineari. La funzione di attivazione potrebbe essere la ReLU o la GELU ad esempio.

Prima e dopo ad ogni transformer block ci sono rispettivamente altri due moduli:

1. Embedding module: consiste di una embedding matrix e di una positional embedding matrix, che convertono rispettivamente i token e la loro posizione nella frase in embedding vectors
2. Output layer: mappa i vettori di output del modello in _token probabilities_ usate per campionare (_sampling_) l'output del modello

![](https://towardsdatascience.com/wp-content/uploads/2021/07/09AyLObnUZmIlLy5a.png)

## Model size

In generale, aumentare il numero di parametri di un modello accresce la sua capacità di imparare. Dati due modelli della stessa identica famiglia, uno con 13 miliardi di parametri molto probabilmente performerà meglio di uno con 7 miliardi di parametri.

Il numero di parametri di un modello ci aiuta anche a stimare le risorse necessarie per trainare o runnare il modello. Ad esempio, se il modello ha 7 milioni di parametri, e ogni parametro viene salvato usando 2 bytes (16 bit), allora la GPU da usare dovrà avere almeno 14 milardi di bytes, ovvero 14 GB. (in realtà, per alcuni tecnicismi, la memoria necessaria potrebbe essere del 20% superiore).

Guardare solo al numero di parametri potrebbe essere fuorviante se il modello è _sparso_, ovvero ha una grande quantità di parametri pari a zero. Ad esempio, uno modello di 7B che è sparso al 90%, ha solo 700M di parametri non nulli. La _sparsity_ permette una gestione più efficiente a livello computazionale: un grande modello sparso potrebbe richiedere meno memoria di un piccolo modello "denso".

Un tipo di modello sparso che ha ottenuto molta popolarità negli ultimi anni è il **mixture-of-experts**. Un modello MoE è diviso in diversi gruppi di parametri e ogni gruppo è un _expert_. A runtime, solo un subset degli esperti viene attivato per processare ogni token.

#dpKnowledge 