In una situazione di multi utenza, bisogna gestire la concorrenza evitando che l’utente rilevi che l’interfaccia del browser non è responsiva, dobbiamo quindi agire sul tipo di rotte.

> TLDR: `sync-sync`, `async-async`, `async-await thread(sync)`

## Dettagli:

Analizziamo le possibili combinazioni di `rotta-metodo`

- `sync-sync`: c'è un `threadpool` e ad ogni richiesta viene assegnato uno dei thread nella pool. Le operazioni I/O sono comunque ottimizzate, quindi i thread rilasciano la pool e non sono bloccanti, anche se il rilascio potrebbe non avvenire nel momento ottimale.
- `async-async`: c'è un solo `thread` e sei tu programmatore che decidi quando rilasciare il controllo con `await`.
- `sync-async`: uguale a `sync-sync` perché comunque ci sono vari thread.
- `async-sync`: in questo caso il codice sincrono che non può essere reso async va gestito con:
    - il metodo `run_in_threadpool()` di FastAPI che lo esegue in un nuovo thread → utile se devi scalare a pochi utenti contemporanei
    - il metodo `to_thread()` di asyncio che lo esegue in un nuovo thread → analogo a `run_in_threadpool()` ma più leggero
    - Oppure puoi scalare con Dramatiq/Celery, dove ogni servizio sync diventa un container eseguito separatamente → utile se devi scalare a migliaia di utenti contemporanei

## Esempio:

```python
import time
import asyncio
from fastapi import FastAPI
import uvicorn

app = FastAPI()

# FastAPI eseguirà ogni chiamata a questo endpoint in un thread separato.
# Questo significa che time.sleep bloccherà il thread, ma non l'intero server (processo)
@app.get("/test-sync")
def test_sync_with_sync_code(sleep_time: int):
	    time.sleep(sleep_time) #-> non bloccante solo perche gil rilascia le risorse per operazioni i/o o sleep

# FastAPI farà await su ogni chiamata a questo endpoint e si aspetta che
# "gestiamo" il codice async (cioè chiamando codice bloccante con "await").
# In questo caso, sleep bloccherà il processo dove gira FastAPI, e le altre richieste dovranno aspettare che questa finisca.
@app.get("/test-async")
async def test_async_with_sync_code(sleep_time: int):
    time.sleep(sleep_time)

# FIX
from fastapi.concurrency import run_in_threadpool

@app.get("/test-async")
async def test_async_with_sync_code(sleep_time: int):
    res = await run_in_threadpool(time.sleep, sleep_time)

# FastAPI farà await su ogni chiamata a questo endpoint e si aspetta che
# "gestiamo" il codice async (cioè chiamando codice bloccante con "await").
# In questo caso usiamo una funzione sleep async e la attendiamo.
# Quando facciamo await restituiamo il controllo a FastAPI così altre richieste possono essere gestite.
@app.get("/test-async-with-async-code")
async def test_async_with_async_code(sleep_time: int):
    await asyncio.sleep(sleep_time)

# Perché dovremmo usare async?
# Le coroutine (codice async) sono molto più leggere rispetto ai thread e hanno meno overhead.
# Questo significa che possiamo gestire più richieste con le stesse risorse.
# Inoltre è più esplicito dove e quando il controllo viene restituito al thread principale.

uvicorn.run(app, host="0.0.0.0", port=8000)

```

## Note:

- Se usiamo una rotta sync, FastAPI riesce comunque a gestire più richieste in parallelo perché usa una threadpool per tutte le rotte sync (introducendo overhead rispetto all'uso di async perché non siamo noi a gestire il controllo)
- È sconsigliato usare rotte async se poi all'interno usi codice bloccante sync, perché se dichiari una rotta async stai assumendo che gestirai tu tutte le await dato che siamo in un single thread. Questo comporta che il main event loop verrà bloccato non appena eseguirà codice SYNC dentro una rotta ASYNC.
- **DB**: Per usare vero asincronismo, sostituiamo le librerie che stiamo usando con versioni async solo se ci sono molte operazioni CRUD, altrimenti lasciamo le librerie originali ma in rotte sync.
- **FILE**: Non cambia molto, sia async che sync usano la threadpool a basso livello.

#dpKnowledge 