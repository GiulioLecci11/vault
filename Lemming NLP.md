#AI 
[[LLM generic]]
La lemmatizzazione è una tecnica di elaborazione del linguaggio naturale (NLP) che consiste nel ridurre le parole alla loro forma base o "lemma". A differenza dello stemming (che taglia semplicemente la fine delle parole), la lemmatizzazione tiene conto della morfologia linguistica e produce sempre parole reali esistenti nel dizionario.

**Riassunto dell'articolo:** https://rachelke411.medium.com/stemming-and-lemming-with-nltk-ae2b366075fe

L'articolo spiega le differenze tra stemming e lemmatizzazione utilizzando la libreria NLTK di Python:

1. **Stemming**: È un processo più semplice e veloce che taglia la fine delle parole per ridurle a una forma comune. L'articolo mostra esempi con l'algoritmo Porter Stemmer, che trasforma parole come "running", "runs" e "runner" in "run". Tuttavia, lo stemming può produrre parole non esistenti (ad esempio "troubl" da "trouble").
2. **Lemmatizzazione**: È un processo più sofisticato che converte le parole alla loro forma base (lemma) tenendo conto della parte del discorso. L'articolo dimostra come utilizzare WordNet Lemmatizer di NLTK, che trasforma correttamente parole come "better" in "good" e "ran" in "run".
3. L'articolo include esempi di codice Python che mostrano l'implementazione di entrambe le tecniche e confronta i risultati ottenuti.
4. Conclude osservando che la scelta tra stemming e lemmatizzazione dipende dalle esigenze specifiche: lo stemming è più veloce ma meno accurato, mentre la lemmatizzazione produce risultati più precisi ma richiede più risorse computazionali.