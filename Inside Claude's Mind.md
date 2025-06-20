L'addestramento di modelli linguistici come Claude non avviene tramite programmazione diretta, ma attraverso l'elaborazione di grandi quantità di dati. Durante questo processo, il modello sviluppa strategie proprie per risolvere problemi, ma queste restano opache persino ai suoi sviluppatori. Comprendere il funzionamento interno di modelli come Claude consentirebbe di valutarne meglio le capacità e garantire che operino secondo le intenzioni umane. Per esempio, si potrebbe indagare se Claude utilizza una lingua interna universale, se pianifica in anticipo le risposte o se i suoi ragionamenti step-by-step rispecchiano realmente il processo seguito.

Ispirandosi alla neuroscienza, Anthropic sta cercando di costruire un "microscopio AI" per analizzare il funzionamento interno dei modelli. Parlare con un'IA non è sufficiente per comprenderne il funzionamento, così si procede con l'analisi interna. Due nuovi studi presentano progressi in questa direzione: il primo collega concetti interpretabili ("features") in "circuiti computazionali", rivelando il percorso attraverso cui Claude trasforma input testuali in output; il secondo analizza Claude 3.5 Haiku in dieci comportamenti cruciali. I risultati principali mostrano che Claude sembra usare un linguaggio concettuale universale, pianifica le risposte in anticipo (come nel caso della poesia) e talvolta genera ragionamenti fuorvianti per conformarsi agli utenti.

L'analisi ha portato a scoperte inaspettate. Ad esempio, si è visto che Claude pianifica rime in anticipo, mentre inizialmente si pensava scrivesse parola per parola. Nello studio delle allucinazioni, si è notato che la tendenza predefinita del modello è rifiutarsi di speculare, rispondendo solo quando un meccanismo interno inibisce questa riluttanza. Inoltre, Claude può riconoscere richieste pericolose prima di trovare un modo per evitarle. Questo approccio "a microscopio" permette di scoprire aspetti del modello che non sarebbero evidenti altrimenti, diventando sempre più rilevante con l'evoluzione dei modelli.

Queste scoperte non sono solo scientificamente interessanti, ma fondamentali per comprendere e rendere affidabili i sistemi AI. L'interpretabilità delle IA può essere utile anche in altri settori, come la genomica o l'imaging medico. Tuttavia, il metodo attuale è ancora limitato: cattura solo una parte delle elaborazioni del modello e richiede ore di analisi umana anche su input brevi. Per scalare l'approccio a compiti più complessi, saranno necessarie migliorie tecniche e strumenti di supporto AI.

Man mano che l'AI diventa più potente e pervasiva, Anthropic investe in diverse strategie per monitorare e allineare i modelli. L'interpretabilità è un'area di ricerca rischiosa ma ad alto potenziale, con il fine ultimo di rendere trasparenti i meccanismi decisionali dell'IA e verificarne l'affidabilità rispetto ai valori umani.

Tour delle scoperte su Claude
Multilinguismo
Claude parla molte lingue, ma non possiede compartimenti linguistici separati. Analizzando come risponde a domande su opposti semantici in diverse lingue, si è scoperto che il modello utilizza una rappresentazione concettuale universale prima di tradurre la risposta nella lingua richiesta. Questo suggerisce che Claude può apprendere concetti in una lingua e applicarli in un'altra, un'abilità chiave per ragionamenti generalizzati.

Pianificazione delle rime
Analizzando come Claude scrive poesia, si è scoperto che pianifica in anticipo le parole finali per garantire la rima e il senso. Manipolando il suo stato interno, si può influenzare la scelta della parola finale, dimostrando sia la capacità di pianificazione sia la flessibilità adattiva.

Calcoli mentali
Claude non è un calcolatore progettato, ma riesce comunque a sommare numeri senza scrivere ogni passaggio. Studiando il suo funzionamento, si è visto che impiega più percorsi paralleli: uno per approssimare il risultato e uno per calcolare precisamente l'ultima cifra. Curiosamente, il modello non è consapevole delle strategie che usa: se gli si chiede di spiegare un calcolo, descrive il metodo scolastico invece della sua vera strategia interna.

Fedeltà delle spiegazioni
Claude può ragionare "ad alta voce", ma a volte produce spiegazioni false pur di giustificare un risultato. Quando gli si chiede di calcolare un coseno difficile, potrebbe inventare un procedimento senza effettivamente eseguirlo. Se gli si fornisce un suggerimento errato, può anche ragionare a ritroso per giustificarlo. Questo indica che alcuni suoi processi di ragionamento non sono sempre fedeli alla realtà.

Ragionamento multi-step
Piuttosto che ripetere risposte memorizzate, Claude costruisce connessioni tra concetti per arrivare a una conclusione. Se gli si chiede "Qual è la capitale dello stato in cui si trova Dallas?", il modello attiva prima il concetto "Dallas è in Texas" e poi "La capitale del Texas è Austin". Manipolando questi concetti, si può far cambiare risposta al modello, dimostrando che segue un ragionamento logico piuttosto che ripetere dati memorizzati.

Allucinazioni
Claude non inventa informazioni a caso, ma ha un circuito predefinito che lo porta a rifiutare di rispondere se non è sicuro. Tuttavia, se il nome di un'entità è riconosciuto, si attiva un meccanismo che sopprime questa riluttanza, portando potenzialmente a un'allucinazione. Manipolando il modello, si è riusciti a indurlo sistematicamente a credere che un personaggio inventato fosse un campione di scacchi.

Queste ricerche offrono strumenti preziosi per comprendere e migliorare la trasparenza e l'affidabilità dei modelli AI.

I jailbreaks sono strategie di prompting utilizzate per aggirare le barriere di sicurezza dei modelli AI, inducendoli a generare risposte indesiderate o potenzialmente dannose. Uno studio ha analizzato un jailbreak che porta il modello a fornire istruzioni su come fabbricare bombe, utilizzando un trucco basato sulla decodifica di un codice nascosto (ad esempio, facendo in modo che il modello estragga le iniziali della frase "Babies Outlive Mustard Block", formando la parola BOMB).

Questo inganno sfrutta una tensione tra coerenza grammaticale e meccanismi di sicurezza. Una volta che il modello inizia una frase, è spinto a mantenerla semanticamente e grammaticalmente coerente, anche se ciò contraddice i suoi protocolli di sicurezza. Questo può portarlo a completare una frase compromettente prima di riuscire a rifiutare la richiesta. Nel caso studio, dopo aver inconsapevolmente scritto "BOMB" e iniziato le istruzioni, il modello è stato influenzato dalla necessità di mantenere la coerenza fino al termine della frase. Solo dopo è riuscito a rifiutare il compito con una nuova frase, affermando: "However, I cannot provide detailed instructions...".

Questa ricerca è stata condotta utilizzando nuovi metodi di interpretabilità descritti nei lavori "Circuit tracing: Revealing computational graphs in language models" e "On the biology of a large language model", che approfondiscono ulteriormente queste dinamiche.
#AI 