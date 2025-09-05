In Python, solo **UN** thread (per processo) alla volta può usare la CPU.

**TLDR** Possiamo usare:

- **Thread**: creano più threads (con memoria condivisa) e poi fanno context switch tra di loro (sempre uno alla volta) → hanno più risorse al loro interno e quindi il context switch è più pesante. UTILE SOLO se hai operazioni CPU-bound.
- **Asyncio**: crea un singolo thread che gestisce più subroutine che cooperano (sempre una alla volta) ma con overhead minore rispetto ai thread perché sono più leggere → lo scheduler non decide lui quando cambiare, sei tu programmatore che con await decidi al posto dello scheduler. UTILE PER GESTIRE MOLTEPLICI RICHIESTE e scalare bene.

---

### Thread

**I thread** sono come lavoratori separati che condividono la memoria ma possono essere messi in pausa e ripresi dal sistema operativo.

- **Come funzionano:** Il sistema operativo crea e gestisce questi workers, decidendo quando ognuno può essere eseguito
- **Parallelismo:** I thread possono lavorare contemporaneamente, ma solo quando eseguono operazioni che rilasciano il GIL
- **Utili per:** Operazioni che attendono molto (come il download di file) o che usano estensioni C che rilasciano il GIL
- **Limitazioni:** Il GIL fa sì che il codice Python puro non possa usare più core della CPU contemporaneamente
- **Uso di risorse:** Ogni thread richiede memoria e il passaggio da uno all'altro richiede tempo

```python
from concurrent.futures import ThreadPoolExecutor
import requests

def download_file(url):
    # Questa operazione attende la rete e rilascia il GIL
    return requests.get(url).content

# Crea effettivamente lavoratori separati (thread), poi solo uno alla volta può usare la CPU! (GIL)
with ThreadPoolExecutor(max_workers=10) as executor:
    results = executor.map(download_file, urls)
```

### Asyncio

**Asyncio** è come avere un solo lavoratore che passa tra molti compiti ogni volta che uno deve attendere qualcosa.

- **Come funziona:** Tutto gira su un singolo thread e i task si sospendono volontariamente quando sono in attesa
- **Concorrenza:** I task si alternano ma non vengono eseguiti davvero contemporaneamente
- **Utili per:** Programmi che fanno molta attesa (come la gestione di molte richieste web)
- **Vantaggi:** Usa pochissima memoria, è più prevedibile, non ha problemi di GIL
- **Uso di risorse:** Molto efficiente - può gestire migliaia di task

Esempio 1:

```python
import asyncio
import aiohttp

async def download_file(url):
    # Questa istruzione sospende la funzione e permette ad altri task di eseguire
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.read()

# Un lavoratore che gestisce più task
async def main():
    tasks = [download_file(url) for url in urls]
    results = await asyncio.gather(*tasks)

asyncio.run(main())
```

Esempio 2

```python
# parallelismo sulle istanze + async
tasks = []

for commercial_conditions_cluster in commercial_conditions_clusters:
    cluster_agent = BaseClusterAgent(doc, commercial_conditions_cluster)
    tasks.append(cluster_agent.call_cluster_agent())

cluster_result = await asyncio.gather(*tasks) # gather crea parallelismo e aggrega automaticamente i risultati
```

---

### Quando usare cosa

- **Usa i Thread** quando:
    - Lavori con librerie normali che non supportano asyncio
    - Devi fare poche cose contemporaneamente (fino a qualche decina)
    - Usi librerie con codice C che rilasciano il GIL
- **Usa Asyncio** quando:
    - Gestisci molte connessioni contemporaneamente (centinaia o migliaia)
    - Usi librerie moderne con supporto async (come aiohttp, asyncpg)
    - Vuoi la massima efficienza nell'uso della memoria

![[Pasted image 20250905145328.png]]

#dpKnowledge 