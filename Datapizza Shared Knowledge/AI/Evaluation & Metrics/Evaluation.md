## Exact Evaluation[.](https://mlops.systems/posts/2025-01-21-notes-on-ai-engineering-chip-huyen-chapter-3.html#exact-evaluation)

Esistono due approcci principali:

1. **Evaluation della Correttezza Funzionale**
    - Valuta se LLM riesce a completare con successo i compiti assegnati
    - Si concentra sulla capacità pratica piuttosto che su metriche teoriche
    - Esempio: Nei task di coding, si verifica se il codice generato supera tutti gli unit test
    - Fornisce misure di successo chiare e oggettive
2. **Calcolo della Similarità rispetto a Dati di Riferimento** Sono identificati quattro metodi distinti:
    1. **Valutazione Umana**
        - Richiede il confronto manuale dei testi da parte di valutatori umani
        - Altamente accurata ma richiede tempo e risorse
        - Scalabilità limitata a causa dell’intervento umano
        - Spesso considerata il gold standard nonostante i limiti
    2. **Verifica di Corrispondenza Esatta**
        - Confronta la risposta generata con le risposte di riferimento per corrispondenze esatte
        - Più efficace con output brevi e specifici
        - Meno utile per output lunghi o creativi
        - Fornisce risultati binari sì/no
    3. **Similarità Lessicale**
        - Usa metriche consolidate come BLEU, ROUGE e METEOR
        - Si concentra sulla sovrapposizione di parole e sulle somiglianze strutturali
        - Riconosciuta come una valutazione un po’ grezza
        - Ampiamente usata per la facilità di implementazione, nonostante i limiti
    4. **Similarità Semantica**
        - Utilizza embedding per confrontare il significato testuale
        - Meno sensibile alle scelte lessicali rispetto agli approcci lessicali
        - La qualità dipende interamente dall’algoritmo di embedding sottostante
        - Può richiedere risorse computazionali significative
        - In generale offre un confronto più sfumato rispetto ai metodi lessicali

Il capitolo include una breve ma rilevante digressione sugli embedding e la loro importanza nella valutazione, anche se sembra un po’ fuori contesto rispetto al resto.

## AI as Judge

Questa sezione esplora l’approccio sempre più diffuso di usare sistemi di AI per valutare altri sistemi di AI.

### Vantaggi

- Decisamente più veloce rispetto alla valutazione umana
- Generalmente più economico su larga scala
- Studi hanno mostrato una forte correlazione con le valutazioni umane in molti casi
- I giudici AI possono fornire spiegazioni dettagliate delle loro decisioni
- Offre maggiore flessibilità negli approcci di valutazione
- Permette valutazioni sistematiche e consistenti su larga scala.

### Tre Approcci Principali

1. **Valutazione della Singola Risposta**
    - Valuta la qualità della risposta basandosi solo sulla domanda originale
    - Spesso implementa sistemi di punteggio numerico (es: scala 1-5)
    - Valuta le risposte in isolamento senza confronto
2. **Confronto con Risposta di Riferimento**
    - Valuta la risposta generata rispetto a risposte di riferimento consolidate
    - Produce solitamente risultati binari (vero/falso)
    - Aiuta a garantire che le risposte soddisfino criteri specifici
3. **Confronto tra Risposte Generate**
    - Confronta due risposte generate per determinarne la qualità relativa
    - Predice le preferenze probabili dell’utente tra le opzioni
    - Particolarmente utile per:
        - Allineamento post-training
        - Ottimizzazione del calcolo in fase di test
        - Ranking dei modelli tramite valutazione comparativa
        - Generazione di dati di preferenza

#dpKnowledge 