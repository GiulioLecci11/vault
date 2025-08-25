La perplexity è l'esponenziale dell'[Entropy](https://www.notion.so/Entropy-24e04d204fa2808493fcc09d94c5657a?pvs=21) e della [Cross Entropy](https://www.notion.so/Cross-Entropy-24e04d204fa28053874ed62a4ccf755d?pvs=21).

Se la cross entropy misura **quanto è difficile per un modello predire il token successivo**,

la perplexity misura la **quantità di incertezza** che il modello ha nella predizione del token.

Una maggiore incertezza (→ maggiore perplexity) significa che ci sono più opzioni possibili tra cui scegliere per il prossimo token.

### Interpretation and use cases

- Dati più strutturati porteranno a una minore perplexity attesa. Ad esempio, il codice HTML è più "predicibile" di un plain text.
- Più grande è il vocabolario, maggiore sarà la perplexity. Intuitivamente, maggiori sono i token a disposizione, maggiore sarà la difficoltà per il modello di "scegliere" quello giusto
- Maggiore la context length (ovvero il testo che prende in input un modello per iniziare la sua predizione), minore sarà la perplexity.

Per dare un'interpretazione numerica, se un modello ha una perplexity pari a 3 e lavora su un vocabolario in cui tutti i token hanno la stessa probabilità di essere scelti (approssimando), significa che il modello ha 1/perplexity = 1/3 probabilità di predire il token corretto.

La perplexity è un buon proxy per capire le capacità di un modello. Se un modello è poco accurato a predire il prossimo token (-> ha alta perplexity), probabilmente le sue capacità nei "downstream task" (summarization, classification, question answering) saranno più scadenti.

#dpKnowledge 
[[Entropy]] [[Cross Entropy Loss]]
