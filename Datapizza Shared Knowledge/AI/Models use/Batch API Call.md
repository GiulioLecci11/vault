Una **Batch API** permette di inviare più richieste in un'unica chiamata verso un **Large Language Model (LLM)**.

Invece di mandare una richiesta alla volta, si raggruppano più input (batch) per ridurre:

- il numero totale di chiamate HTTP
- la latenza complessiva
- i costi di rete

### Vantaggi principali

- **Efficienza**: meno overhead di comunicazione
- **Velocità**: tempo di elaborazione ridotto per grandi volumi
- **Scalabilità**: migliore gestione di carichi elevati

> NB: non tutti i provider di LLM supportano nativamente il batch processing; verifica le specifiche dell'API.

In pratica, una chiamata batch invia un array di prompt e riceve un array di risposte, invece di processare ogni prompt separatamente.

#dpKnowledge 