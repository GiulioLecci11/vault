RETRIEVAL:
Il retrieval è il processo di recupero dei documenti più pertinenti da una base documentale in risposta a una query. Esso quindi fornisce il contesto al modello generativo, che poi usa le informazioni per formulare una risposta accurata.
Esso può essere:
1) Sincrono: In un retrieval sincrono, il sistema recupera i documenti pertinenti in tempo reale, nel momento in cui viene ricevuta una query dall'utente. Ciò assicura che i documenti siano freschi e aggiornati, ma introduce una maggiore latenza
2) Asincrono: In un retrieval asincrono, il sistema pre-calcola le query più probabili o indicizza i documenti in modo da ridurre il tempo di ricerca. Ciò riduce il carico computazionale all'interazione con l'utente e aumenta la velocità


COME VIENE RAPPRESENTATO IL TESTO:
Bag of Words (BoW): Rappresenta un testo come un insieme di parole (vettori) senza considerare l’ordine. In questo caso ogni parola ha un peso basato sulla frequenza con cui appare e non ne viene catturato il significato semantico (gatto e felino non hanno nessun collegamento). Utile solo su testi brevi.

Dense Embeddings: Rappresentano il testo come un vettore denso in uno spazio ad alta dimensionalità, catturando relazioni semantiche. Solitamente si usano dei modelli pre-addestrati come BERT per creare questi embeddings. In questo modo parole come gatto e felino risultano vicine nello spazio vettoriale poiché correlate.
Ideale per retrieval semantico, dove è importante il significato delle query e dei documenti.


PERFORMANCE METRICS:
Precision: Indica quanti dei documenti recuperati sono effettivamente pertinenti rispetto alla query. (Se recuperi 10 documenti e 7 sono pertinenti, la precisione è 70%.)

Recall: Indica quanti dei documenti pertinenti presenti nella base sono stati effettivamente recuperati. (Se ci sono 20 documenti pertinenti nella base e ne recuperi 14, il recall è 70%.)

Si dovrebbe trovare un tradeoff tra le due in quanto aumentare il numero di documenti recuperati migliora il recall, ma potrebbe ridurre la precisione (più documenti irrilevanti).

PERFORMANCE SCORES:
BLEU (Bilingual Evaluation Understudy) da non confondere con GLUE: Valuta la similarità tra la risposta generata da un modello e un set di risposte di riferimento, misura quindi quanto una risposta è simile a risposte umane ma NON tiene conto della semantica quindi potrebbe dare punteggi bassi a risposte formulate in modo diverso.

ROUGE (Recall-Oriented Understudy for Gisting Evaluation): Misura quanto testo della risposta generata coincide con il testo delle risposte di riferimento, utile per valutare quanto della risposta generata copra le informazioni attese.

Factuality Score: Misura quanto la risposta generata sia basata su fatti corretti (quanto sia fattuale) rispetto ai documenti o al contesto fornito. Un factuality score basso indica quindi la presenza di <<Hallucinations>>

principalmente fanno rag, chatbot e speech to speech collegate ad una rag
#AI 
[[LLM generic]]
[[Vectors & vector spaces]]
[[Embeddings & Context Window]]
