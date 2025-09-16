Quando dobbiamo svolgere delle pipeline per cui poi il controllo va subito ritornato all'utente, possiamo usare:

- Celery/Dramatiq
- Background Tasks

---

# Celery

**Celery** è un sistema di code di attività utilizzato per eseguire lavori in background su processi worker separati — anche su altre macchine.

- **Come funziona:** Definisci delle attività (normali funzioni Python), poi Celery le mette in una coda e un processo worker le esegue in modo asincrono
- **Parallelismo:** I worker possono funzionare in più processi o macchine in parallelo, aggirando il GIL. Puoi scalare a più macchine!
- **Utile per:** Attività di lunga durata o programmate (come inviare email, processare immagini, o lavori di ML)
- **Vantaggi:** Tollerante ai guasti, supporta nativamente i retry, funziona al di fuori del ciclo richiesta/risposta HTTP
- **Utilizzo risorse:** I worker sono processi separati; l'utilizzo di memoria e CPU dipende dal carico delle tue attività
- **Broker necessario:** Richiede un message broker (Redis, RabbitMQ) per gestire le code

```python
# tasks.py
from celery import Celery
app = Celery("tasks", broker="redis://localhost:6379/0")

@app.task
def process_data(data):
    # Questa è una normale funzione sincrona
    result = some_expensive_computation(data)
    return result

```

```python
# main.py
from tasks import process_data

# Questo mette l'attività in coda in background
process_data.delay(my_data)

# Puoi anche ottenere il risultato in modo asincrono
result = process_data.delay(my_data)
result.get()  # Blocca finché non è completato

```

> Celery non supporta async def nativamente, motivo per cui Datapizza sta sperimentando con Dramatiq.

---

# Dramatiq

**Dramatiq** è un'alternativa moderna a Celery che supporta nativamente le funzioni async.

```python
import dramatiq
from dramatiq.brokers.redis import RedisBroker

broker = RedisBroker(host="localhost", port=6379, db=0)
dramatiq.set_broker(broker)

@dramatiq.actor
async def process_data_async(data):
    # Supporta nativamente async/await
    result = await some_async_expensive_computation(data)
    return result
```

---

# Background Tasks

**BackgroundTasks** è una funzionalità semplice in FastAPI per eseguire un'attività **dopo** aver restituito la risposta HTTP.

- **Come funziona:** Esegue l'attività in un nuovo thread dopo che la risposta è stata inviata
- **Parallelismo:** Ancora soggetto al GIL; solo un thread usa la CPU alla volta
- **Concorrenza:** Possibile, i task async si cedono tra di loro il controllo. Inoltre, anche TRA più background tasks si può avere concorrenza sempre con async
- **Utile per:** Attività leggere "fire-and-forget" (es., inviare email, logging)
- **Limitazioni:**
    - Non persistenti. Se l'app crasha, l'attività viene persa
    - Non puoi neanche interrompere un task
    - Nessun meccanismo di retry automatico
    - Limitato alla vita dell'applicazione
- **Utilizzo risorse:** Minimo, ma non adatto per attività pesanti o critiche

### Esempio tipico di workflow:

1. Mandi la richiesta in background
2. Ricevi risposta 200 OK
3. Quando il processo finisce aggiorna un db
4. Utente fa polling su db per vedere quando ha terminato
5. Utente da db si scarica il risultato dell'operazione

```python
from fastapi import FastAPI, BackgroundTasks
import asyncio

app = FastAPI()

def send_email(email: str, message: str):
    print(f"Sending email to {email}: {message}")

@app.post("/send/")
def send(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(send_email, email, "Welcome!")
    return {"message": "Email scheduled"}

```

---

### Quando Usare Ciascuno

- **Usa Celery/Dramatiq** quando:
    - Hai bisogno di eseguire lavori di background di lunga durata
    - Le attività dovrebbero funzionare indipendentemente dalle richieste HTTP
    - Vuoi meccanismi di retry, programmazione, o worker distribuiti
    - Necessiti di persistenza delle attività (sopravvivono al riavvio dell'app)
    - Vuoi monitoraggio e debugging avanzati
    - Hai bisogno di scalabilità orizzontale
- **Usa Background Tasks quando:**
    - Hai bisogno di rispondere rapidamente a una richiesta e fare qualcosa di leggero dopo
    - Va bene che le attività non siano persistenti
    - Non hai bisogno di elaborazione distribuita o esecuzione di lunga durata
    - L'attività è semplice e non critica per il business
    - Non vuoi la complessità aggiuntiva di un message broker

### Confronto Rapido

|Caratteristica|Celery/Dramatique|Background Tasks|
|---|---|---|
|**Persistenza**|✅|❌|
|**Retry automatici**|✅|❌|
|**Scalabilità**|✅|❌|
|**Complessità setup**|🟡 Media (broker richiesto)|✅ Minima|
|**Monitoring**|✅|❌|
|**Async support**|🟡 Dramatique sì, Celery no|✅ Sì|

## Esempio ABB:

TODO

#dpKnowledge 