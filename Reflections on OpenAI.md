#miscellaneous 

L'autore dell'articolo condivide le sue riflessioni dopo aver lavorato in OpenAI per circa un anno, da maggio 2024. Lo scopo non è rivelare segreti industriali, ma descrivere la cultura e l'atmosfera di una delle organizzazioni più affascinanti del momento. La sua decisione di andarsene non è legata a conflitti personali, ma alla difficoltà di passare dall'essere un fondatore di una sua startup al diventare un dipendente in un'azienda di 3.000 persone.

#### Cultura Aziendale
La cultura di OpenAI è definita da alcuni elementi chiave:

1.  **Crescita Esplosiva:** In un solo anno, l'azienda è passata da 1.000 a oltre 3.000 dipendenti. Questa rapida scalata ha messo sotto pressione ogni aspetto organizzativo: comunicazione, struttura gerarchica, processi di assunzione e sviluppo dei prodotti.
2.  **Comunicazione su Slack:** Tutta la comunicazione interna avviene su Slack (il nostro discord😂). Non esistono quasi email. Questo sistema può essere fonte di distrazione se non gestito con cura, ma funziona se si curano i canali e le notifiche.
3.  **Approccio "Bottom-Up":** Le idee migliori possono venire da chiunque, specialmente nella ricerca. Non esiste un "piano generale" calato dall'alto; il progresso è iterativo e si basa sui frutti delle nuove scoperte. Questo rende l'ambiente molto meritocratico: l'esecuzione e la qualità delle idee contano più delle manovre politiche.
4.  **Iniziativa Individuale:** C'è una forte spinta all'azione. I dipendenti sono incoraggiati a sviluppare le proprie idee e prototipi senza chiedere permessi. Spesso, più team lavorano su idee simili in parallelo, e le iniziative più promettenti attraggono rapidamente nuove risorse. I ricercatori sono visti come "mini-manager" di se stessi.
5.  **Agilità e Adattabilità:** OpenAI è capace di cambiare direzione molto rapidamente in base a nuove informazioni, un'agilità notevole per un'azienda delle sue dimensioni.
6.  **Pressione e Segretezza:** L'azienda è sotto costante scrutinio da parte di media, governi e competitor (Meta, Google, Anthropic). Questo crea un'atmosfera seria e molto riservata. L'autore stesso non poteva parlare in dettaglio del suo lavoro all'esterno.
7.  **Missione e Intenti:** Nonostante le critiche, l'autore è convinto che i dipendenti siano sinceramente motivati a "fare la cosa giusta", spinti dall'obiettivo di creare un'IA generale (AGI) benefica e dalla responsabilità di gestire un prodotto usato da milioni di persone per questioni delicate.
8.  **Disponibilità dei Modelli:** Un punto di forza è la volontà di rendere i modelli più avanzati accessibili a tutti tramite API e ChatGPT, invece di riservarli a clienti enterprise costosi.
9.  **Sicurezza:** La sicurezza è una priorità, con un'enfasi maggiore sui rischi pratici (incitamento all'odio, disinformazione, armi biologiche) rispetto a quelli teorici (esplosione di intelligenza), anche se entrambi i campi sono presidiati.
10. **Costi delle GPU:** Quasi ogni costo aziendale è trascurabile se paragonato a quello delle GPU, necessarie per l'addestramento e l'esecuzione dei modelli.

#### Codice e Infrastruttura
1.  **Stack Tecnologico:** L'azienda usa un "monorepo" (un unico grande archivio di codice) principalmente in Python. La qualità del codice varia molto, andando da notebook Jupyter sperimentali a librerie complesse scritte da veterani di Google. Le tecnologie più comuni sono FastAPI per le API e Pydantic per la validazione.
2.  **Infrastruttura su Azure:** OpenAI opera interamente su Microsoft Azure. Tuttavia, l'autore nota che solo pochi servizi di Azure sono considerati pienamente affidabili (Kubernetes, CosmosDB, BlobStore), portando a una forte tendenza a costruire soluzioni interne ("in-house").
3.  **Influenza di Meta (Facebook):** C'è un notevole flusso di ingegneri provenienti da Meta. Questo ha portato OpenAI ad assomigliare alla Meta dei primi tempi: un'app di successo virale, un'infrastruttura ancora in via di sviluppo e una cultura del "muoversi velocemente". Diverse soluzioni infrastrutturali interne ricordano quelle di Meta (es. una re-implementazione di TAO (il sistema di dati su cui Meta (Facebook) ha costruito il suo "social graph", ovvero l'enorme database che mappa le connessioni tra utenti, post, foto e interessi. L'autore nota come OpenAI stia costruendo un sistema simile internamente.)).
4.  **Centralità della "Chat":** Dopo il successo di ChatGPT, gran parte della base di codice è strutturata attorno ai concetti di "messaggio" e "conversazione".

#### Il Lancio di Codex
L'autore descrive il lancio di Codex (un agente IA per la programmazione) come uno dei momenti più alti della sua carriera.
*   **Sprint di 7 settimane:** L'intero prodotto è stato costruito da zero e lanciato in sole sette settimane, un ritmo che l'autore definisce incredibile. Questo ha richiesto un lavoro intenso, con orari prolungati e weekend inclusi.
*   **Team Efficace:** Il successo è stato possibile grazie a un piccolo team di persone molto esperte (circa 8 ingegneri, 4 ricercatori, designer e altri ruoli) che non avevano bisogno di molta direzione ma di grande coordinamento.
*   **Filosofia del Prodotto:** Codex è stato progettato per funzionare in modo asincrono, come un "collega" a cui si affida un compito per poi ricevere una Pull Request (PR) completa.
*   **Impatto:** L'autore sottolinea l'impatto straordinario del prodotto, citando i dati pubblici sulle centinaia di migliaia di PR generate da Codex in poco tempo.

#### Considerazioni Finali
Riflettendo sul suo anno in OpenAI, l'autore ritiene che sia stata una delle migliori decisioni della sua vita, avendo imparato moltissimo sui modelli, sulle persone e su come lanciare prodotti di grande impatto. Conclude vedendo la corsa verso l'AGI come una gara a tre tra **OpenAI, Anthropic e Google**, ciascuna con il proprio DNA e approccio distintivo.