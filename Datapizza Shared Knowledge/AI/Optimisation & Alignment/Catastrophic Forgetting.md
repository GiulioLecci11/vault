La tendenza di una [rete neurale](https://en.wikipedia.org/wiki/Artificial_neural_network) a dimenticare in modo improvviso e drastico informazioni apprese in precedenza quando apprende nuove informazioni.

## Esempi Pratici

### 1. Classificazione di Immagini

Hai una rete neurale che ha imparato a classificare **gatti e cani** con un'accuratezza del 95%. Successivamente, vuoi insegnarle a riconoscere anche gli **uccelli**. Durante l'addestramento sui dati degli uccelli, la rete:

- Impara efficacemente a classificare gli uccelli
- **Ma perde drasticamente** la capacità di distinguere gatti e cani (l'accuratezza scende al 30%)

Questo è Catastrophic Forgetting: la rete "sovrascrive" le conoscenze precedenti con quelle nuove.

### 2. Large Language Models

Un modello linguistico addestrato per:

1. **Prima fase**: Rispondere a domande di matematica
2. **Seconda fase**: Scrivere poesie

Dopo la seconda fase, il modello potrebbe dimenticare come risolvere equazioni matematiche, concentrandosi solo sulla composizione poetica.

### 3. Apprendimento Continuo nei Chatbot

Un chatbot aziendale che:

- **Mese 1**: Impara FAQ sul prodotto A
- **Mese 2**: Impara FAQ sul prodotto B
- **Risultato**: Non ricorda più le informazioni sul prodotto A

## Tecniche di Mitigazione

### 1. **Elastic Weight Consolidation (EWC)**

Penalizza i cambiamenti dei parametri importanti per i task precedenti.

### 2. **Rehearsal/Replay**

Mantiene un piccolo dataset dei task precedenti e lo "ripassa" durante l'addestramento del nuovo task.

### 3. **Progressive Neural Networks**

Aggiunge nuovi moduli neurali per ogni nuovo task, preservando quelli precedenti.

### 4. **Memory-Augmented Networks**

Utilizza memorie esterne per conservare informazioni sui task precedenti.

## Perché Accade?

Il Catastrophic Forgetting deriva dal fatto che le reti neurali tradizionali:

- Usano gli stessi parametri (pesi) per tutti i task
- Ottimizzano globalmente questi parametri
- Non hanno meccanismi naturali per "proteggere" le conoscenze precedenti

#dpKnowledge 