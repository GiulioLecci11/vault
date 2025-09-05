TLDR:

> Multithreading : non possibile in Python, solo UN thread alla volta Multi Processing: possibile ma utile solo in caso di task CPU-intensive

Il **Global Interpreter Lock (GIL)** è un coordinatore che assicura che due thread non possano modificare lo stesso valore contemporaneamente. È un lock che permette a **solo un thread** di eseguire codice Python alla volta.

- Il problema è che molte persone hanno cercato di implementare un vero parallelismo su più core, ma il problema era che con thread singoli le prestazioni diminuivano.

## **Perché esiste il GIL?**

Il GIL è stato implementato per semplificare la gestione della memoria in Python e prevenire race conditions. Senza il GIL, sarebbe necessario utilizzare lock molto più complessi per proteggere le strutture dati interne di Python.

## **Quando il GIL viene rilasciato?**

- Durante operazioni I/O (lettura/scrittura file, richieste di rete)
- Chiamate a funzioni C che rilasciano esplicitamente il GIL
- Operazioni che durano a lungo (come `time.sleep()`)

Riguardo al **Multi Processing**:

- Il parallelismo effettivamente lo si può ottenere creando processi separati (al posto dei thread) ma la memoria non è condivisa e ogni volta i dati vanno serializzati e passati tra i vari processi (overhead altissimo)
- Ogni processo ha la propria istanza dell'interprete Python e quindi il proprio GIL
- Utile principalmente per task CPU-intensive dove il beneficio del parallelismo supera l'overhead della comunicazione tra processi

## **Alternative e Soluzioni**

- **asyncio**: per task I/O-bound con programmazione asincrona
- **multiprocessing**: per task CPU-intensive
- **Implementazioni alternative**: PyPy, Jython, IronPython (alcune non hanno il GIL)
- **Estensioni C**: per operazioni critiche in termini di performance

#dpKnowledge 