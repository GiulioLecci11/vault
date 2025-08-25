La **cosine similarity** è una metrica fondamentale nella pipeline RAG, utilizzata per misurare la similarità tra vettori di embedding.

Permette di determinare quanto due testi (ad esempio, una query e un segmento di documento) siano semanticamente vicini, indipendentemente dalla loro lunghezza.

## Cos'è la Cosine Similarity?

La cosine similarity misura il coseno dell’angolo tra due vettori nello spazio n-dimensionale.

Il valore risultante è compreso tra -1 e 1:

- **1** indica che i vettori sono identici (stesso significato).
- **0** indica che sono ortogonali (nessuna correlazione semantica).
- **1** indica che sono opposti (significato opposto, caso raro negli embeddings testuali).

La formula è:

$$

\text{cosine\_similarity}(A, B) = \frac{A \cdot B}{\|A\| \|B\|}

$$

dove:

$A$ e $B$ sono i vettori di embedding,

$A \cdot B$ è il prodotto scalare

$||A||$ e $||B||$ sono le norme (lunghezze) dei vettori.

## Utilizzo nella Pipeline RAG

1. **Embedding**
    - Sia la query dell’utente che i segmenti di documento vengono trasformati in vettori di embedding.
2. **Calcolo della Similarità**
    - Per ogni documento, si calcola la cosine similarity tra il suo embedding e quello della query.
3. **Retrieval**
    - I documenti con la cosine similarity più alta rispetto alla query vengono considerati i più rilevanti e selezionati per la fase successiva (ad esempio, reranking o generazione della risposta).

## Esempio Pratico

- **Query:** “Come funziona la fotosintesi?”
- **Documento 1:** “La fotosintesi clorofilliana è il processo con cui le piante producono energia.”
- **Documento 2:** “La Rivoluzione Francese iniziò nel 1789.”

Dopo aver calcolato gli embeddings, la cosine similarity tra la query e il Documento 1 sarà molto più alta rispetto al Documento 2, perché sono semanticamente più vicini.

## Perché è Importante?

- Permette di confrontare testi in modo semantico, non solo lessicale.
- È efficiente da calcolare anche su grandi dataset.
- È la base per la ricerca vettoriale e il retrieval semantico nella pipeline RAG.

---

**In sintesi:**

La cosine similarity misura quanto due vettori di embedding sono orientati nella stessa direzione, permettendo di identificare i documenti più rilevanti rispetto a una query nella pipeline di Retrieval-Augmented Generation.

#dpKnowledge 