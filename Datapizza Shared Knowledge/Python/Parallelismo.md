- Multithreading : Non supportato mai in python, solo **UN** thread per core a causa del GIL. Può essere simulato con: `Threading`, `Asyncio`
- Multi Processing: Supportato

TLDR:

- `Multiprocessing`: solo per compiti CPU-bound senza memoria condivisa.
- `Asyncio`: per IO-bound tasks per cui sei sicuro di avere supporto asincrono. (1 thread che fa context switch in maniera efficiente)
- `Threading`: Se non c'è asyncio disponibile, allora usa i thread. (1 thread che fa context switch)

| Caratteristica         | `threading`                              | `asyncio`                                      | `Multiprocessing`                                           |
| ---------------------- | ---------------------------------------- | ---------------------------------------------- | ----------------------------------------------------------- |
| Vero parallelismo      | ❌ (GIL presente)                         | ❌ (single-threaded, multitasking cooperativo)  | ✅ (ogni processo ha il suo interprete)                      |
| Quando usarlo          | IO-bound tasks con API bloccanti         | IO-bound tasks con API asincrone               | Task CPU-bound (es. calcoli matematici)                     |
| Thread/processi attivi | Un thread attivo alla volta (GIL)        | Singolo thread, molte coroutine                | Più processi in parallelo con un thread attivo per processo |
| Memoria condivisa      | ✅ (condivisa tra i thread)               | ✅ (singolo thread, oggetti condivisi)          | ❌ (ogni processo ha il suo spazio di memoria)               |
| Overhead               | Basso                                    | Bassissimo (niente thread/processi)            | Alto (i processi duplicano stato/memoria)                   |
| Comunicazione          | ✅ Facile (stato condiviso, servono lock) | ✅ Molto facile (contesto condiviso)            | ❌ Più complessa (serve IPC: Pipe/Queue)                     |
| Tempo di avvio         | ⚡ Veloce                                 | ⚡⚡ Molto veloce                                | 🐢 Più lento (overhead di creazione processi)               |
| Modello di concorrenza | Preemptive (OS gestisce i thread)        | Cooperativo (le coroutine cedono il controllo) | Preemptive (OS gestisce i processi)                         |
| Casi d'uso comuni      | Web scraping, UI, I/O bloccante          | Web server, client async, alta concorrenza I/O | ML training, rendering, elaborazione dati pesante           |
| Compatibilità librerie | ✅ Alta (funziona con la maggior parte)   | ⚠️ Solo con librerie compatibili async         | ⚠️ Alcune librerie potrebbero non essere compatibili        |
#dpKnowledge 
