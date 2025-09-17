
Questa guida spiega l'architettura e l'utilizzo del progetto, con focus su:

- Stability Tester: come effettuare test di stabilità di policy-checking via backend senza frontend

- Prompt Genetic Optimizer: come migliorare automaticamente i prompt (task_description) delle Commercial Conditions

- Esecuzione passo-passo, esempi di codice riutilizzabili e consigli per generalizzare

  

---

  

### Panoramica del Progetto

  

- **Entry points**:

- `main.py`: esempio minimale per lanciare il Prompt Genetic Optimizer (async)

- `stability_tester.py`: implementa la classe `StabilityTester` che spara richieste al backend, polla il DB e calcola metriche

- `prompt_genetic_optimizer.py`: implementa `PromptGeneticOptimizer` che usa uno GA guidato da LLM per migliorare i prompt

- **Dipendenze esterne** (richieste dal codice):

- Backend/API "tc-ai" accessibile (FastAPI) con endpoint `/v0/auth/login`, `/v0/auth/me`, `/v0/policy-check/policy-checking-pipeline`

- DB e modelli `tc-ai` disponibili via import (es.: `api.settings.APISettings`, `DBManager`, `CRUD`, modelli ORM)

- Azure OpenAI via `datapizzai.clients.AzureOpenAIClient` (per Prompt Optimizer)

- File PDF in `docs/` per i test di stabilità

  

---

  

### Installazione e Setup

  

- **Prerequisiti**:

- Python 3.10+

- Backend tc-ai avviato e raggiungibile (es.: `http://localhost:8000`)

- Accesso al DB usato dal backend (stessa connessione di `tc-ai`)

- Variabili d'ambiente per Azure OpenAI se si usa l'optimizer

  

- **Ambiente e dipendenze (con uv)**:

  

```bash

# Creazione ambiente

uv venv

source .venv/bin/activate

  

# Installazione pacchetti principali

uv add aiohttp rich pandas python-dotenv werkzeug

  

# Se necessario, aggiungere client Datapizza/Azure

uv add datapizzai

  

# (Opzionale) Se tc-ai è una repo locale adiacente, aggiungere il path ai sys.path

# In questo progetto è automatizzato: i file aggiungono automaticamente tc-ai/src al sys.path

```

  

- **Configurazione env**:

- Copiare/compilare `tc-ai.env` in root con le chiavi richieste da Azure OpenAI e/o DB (se previste dal vostro stack). Il file viene caricato da `prompt_genetic_optimizer.py`.

  

---

  

### Stability Tester: Architettura e Flusso

  

La classe `StabilityTester` incapsula tutto per lanciare N richieste al backend, attendere la loro conclusione (polling su DB) e aggregare i risultati in DataFrame.

  

Principali step:

1. Autenticazione FakeAuth contro `GET /v0/auth/login` (segue redirect) e verifica con `GET /v0/auth/me` (cookie `session_id`).

2. Scansione cartella documenti e invio batch al pipeline endpoint `POST /v0/policy-check/policy-checking-pipeline` con form multipart:

- `commercial_conditions_ids`: CSV con gli ID policy

- `analysis_mode`: "baseline"

- `session_name`: nome sessione

- `files`: PDF allegati

3. Polling sul DB tramite `CRUD`/`DBManager` finché le `CheckSession` raggiungono stato terminale `COMPLETED`/`FAILED` o timeout.

4. Aggregazione in `pandas.DataFrame` con colonne chiave (session_id, document_id, commercial_condition_id, result, reasoning, referenced_pages, reference_content, timestamps).

5. Calcolo metriche di stabilità: coerenza per coppia (document, policy) e stability score complessivo.

  

Estratti di codice chiave:

```1:25:/Users/giuliolecci/Downloads/Cookbooks/stability tester/main.py

# Example usage of the Genetic Algorithm for Prompt Optimization

from prompt_genetic_optimizer import PromptGeneticOptimizer

import asyncio

  
  

async def main():

"""Main async function with proper cleanup."""

# Initialize the genetic optimizer

optimizer = PromptGeneticOptimizer(

max_iterations=3, improvement_threshold=0.03, batch_size=2

)

  

policies = list(set(x for x in range(1, 37)) - set([18, 33]))

optimized_results = await optimizer.run_iterative_optimization(

policies=policies,

doc_folder="/Users/lorenzozanolin/Desktop/Datapizza/work/ABB/stability tester/docs",

num_requests=5,

timeout_sec=3600,

concurrency_prompts=5,

)

  
  

if __name__ == "__main__":

asyncio.run(main())

```

  

```67:90:/Users/giuliolecci/Downloads/Cookbooks/stability tester/stability_tester.py

class StabilityTester:

"""Class wrapper exposing a single public method `test_stability`.

  

Configuration that is not part of the required public method parameters

(e.g., batch_size, poll interval, session name, CSV output path) can be

provided via the constructor.

"""

  

def __init__(

self,

batch_size: int = 2,

poll_interval_sec: int = 15,

session_name: str = "stability",

output_csv: Optional[str] = None,

continue_on_timeout: bool = True,

) -> None:

...

```

  

```818:871:/Users/giuliolecci/Downloads/Cookbooks/stability tester/stability_tester.py

results = await self._poll_sessions(

db_manager,

user_id=user_id,

session_ids=session_ids,

poll_interval_sec=self.poll_interval_sec,

timeout_sec=timeout_sec,

)

cc_prompts = self._get_cc_prompts(db_manager, policies)

  

# Aggregate and optionally export

df = self._aggregate_to_dataframe(results)

if self.output_csv:

out_path = Path(self.output_csv).resolve()

os.makedirs(out_path.parent, exist_ok=True)

df.to_csv(out_path, index=False)

logger.info("Aggregated %d rows saved to %s", len(df.index), out_path)

  

return df, cc_prompts

```

  

---

  

### Eseguire lo Stability Tester senza frontend

  

L'app normalmente ha un frontend. Qui interagiamo solo con il backend FastAPI e il DB.

  

Soluzione 1: CLI di `stability_tester.py` (consigliata per run standalone)

```bash

python /Users/giuliolecci/Downloads/Cookbooks/stability\ tester/stability_tester.py \

--base-url http://localhost:8000 \

--doc-folder "/Users/giuliolecci/Downloads/Cookbooks/stability tester/docs" \

--policies "1,2,4" \

--requests 10 \

--batch_size 3 \

--poll-interval 60 \

--timeout 3600 \

--session-name stability \

--output-csv stability_results.csv \

--log-level DEBUG

```

  

Soluzione 2: Uso programmatico (da un vostro script)

```python

import asyncio

from stability_tester import StabilityTester

  

async def run():

tester = StabilityTester(batch_size=3, poll_interval_sec=30,

session_name="stab-run", output_csv="stability.csv")

results_df, cc_prompts_df = await tester._run_async(

policies="1,2,4",

doc_folder="/Users/giuliolecci/Downloads/Cookbooks/stability tester/docs",

base_url="http://localhost:8000",

num_requests=10,

timeout_sec=3600,

log_level="INFO",

)

# Calcolo metriche (se non avete usato run_stability_test)

metrics = tester._calculate_stability(results_df, cc_prompts_df)

print(metrics.get("stability_score"))

  

asyncio.run(run())

```

  

Note operative:

- Il login è effettuato via FakeAuth `GET /v0/auth/login` con gestione cookie automatica; serve che il backend sia configurato per emettere `session_id` in cookie.

- Il polling legge direttamente dal DB del backend usando `DBManager/CRUD`: lanciare questo script dove può raggiungere lo stesso DB del backend.

- I PDF in `docs/` devono essere accessibili al processo.

  

---

  

### Prompt Genetic Optimizer: Architettura e Flusso

  

`PromptGeneticOptimizer` orchestra cicli iterativi:

1. Lancia Stability Test (riutilizzando `StabilityTester`)

2. Calcola metriche e individua policy incoerenti (`consistency_ratio < threshold`)

3. Raccoglie esempi di reasoning e reference content per quelle policy

4. Chiede a un LLM (Azure OpenAI) di proporre un prompt migliorato per ciascuna policy

5. Aggiorna nel DB il campo `task_description` della `CommercialCondition`

6. Ripete finché miglioramento < soglia o raggiunge `max_iterations`

  

Snippet chiave:

```84:118:/Users/giuliolecci/Downloads/Cookbooks/stability tester/prompt_genetic_optimizer.py

class PromptGeneticOptimizer:

...

def __init__(

self,

db_manager: Optional[DBManager] = None,

max_iterations: int = 5,

improvement_threshold: float = 0.1,

batch_size: int = 2,

):

...

self.stability_tester = StabilityTester(batch_size=batch_size)

self.openai_client = AzureOpenAIClient(...)

self.db_manager = DBManager(APISettings().database_url) if db_manager is None else db_manager

self.crud = CRUD(self.db_manager)

```

  

```429:536:/Users/giuliolecci/Downloads/Cookbooks/stability tester/prompt_genetic_optimizer.py

async def _optimize_prompts(...):

# 1) trova policy incoerenti

cc_ids_to_improve = await asyncio.to_thread(

self._get_inconsistent_commercial_conditions, metrics, consistency_threshold

)

# 2) raccoglie esempi di reasoning

reasoning_examples = await asyncio.to_thread(

self._collect_reasoning_examples, results, cc_ids_to_improve

)

# 3) chiama LLM e 4) aggiorna DB

...

```

  

Esecuzione esempio:

```bash

python /Users/giuliolecci/Downloads/Cookbooks/stability\ tester/prompt_genetic_optimizer.py \

--policies "1,2,3,4,5" \

--doc-folder "/Users/giuliolecci/Downloads/Cookbooks/stability tester/docs" \

--base-url http://localhost:8000 \

--num-requests 10 \

--max-iterations 3 \

--improvement-threshold 0.05 \

--consistency-threshold 0.9 \

--batch-size 2 \

--timeout-sec 3600 \

--concurrency-prompts 5 \

--log-level INFO

```

  

Uso programmatico (come in `main.py`):

```python

import asyncio

from prompt_genetic_optimizer import PromptGeneticOptimizer

  

async def main():

optimizer = PromptGeneticOptimizer(max_iterations=3, improvement_threshold=0.03, batch_size=2)

policies = [1,2,3,4,5]

results = await optimizer.run_iterative_optimization(

policies=policies,

doc_folder="/Users/giuliolecci/Downloads/Cookbooks/stability tester/docs",

base_url="http://localhost:8000",

num_requests=5,

timeout_sec=3600,

log_level="INFO",

consistency_threshold=0.9,

concurrency_prompts=5,

)

print(results)

  

asyncio.run(main())

```

  

Requisiti per l'optimizer:

- Variabili Azure OpenAI in `tc-ai.env` (chiavi, endpoint, model_name, api_version, temperature)

- Connettività al DB per aggiornare `CommercialCondition.task_description`

  

---

  

### Come generalizzare per altri progetti/algoritmi

  

- **Separare I/O da core**: mantenere tre layer netti: orchestrazione (async/batch), integrazione backend (HTTP + cookie), persistenza (CRUD/DB). Interfacciare ciascun layer via classi/astrazioni.

- **Policy-agnostic**: accettare `policies` come `Union[str, List[int]]` e convertirle a CSV in un helper dedicato (già presente `_policies_to_csv`).

- **Batching e backpressure**: usare `batch_size` per evitare saturazione del backend. Introdurre rate-limit se necessario.

- **Polling robusto**: scrivere `_poll_sessions` idempotente, con timeout e progress callback. Evitare query bloccanti, usare `asyncio.to_thread` su CRUD sync.

- **Metriche pluggable**: isolare `_calculate_stability` affinché ritorni sia dettagli (DataFrame) sia metriche aggregate. Per altri domini sostituire solo questa funzione.

- **Retry & logging**: introdurre retry esponenziale con jitter per chiamate LLM o HTTP, con logging strutturato.

- **LLM-agnostic**: incapsulare il client LLM; per passare a OpenAI/Anthropic basta sostituire la factory del client.

- **DB-agnostic**: nascondere ORM dietro a `CRUD`/`DBManager`; per cambiare schema implementare gli stessi metodi minimi (`get_check_session`, `get_checks_for_session`, `get_commercial_conditions`).

- **Reproducibilità**: salvare CSV aggregati, versionare i prompt e loggare parametri run (policies, doc_folder, seeds, tempi).

  

Pattern riutilizzabili (boilerplate):

```python

# Retry con backoff + jitter

import asyncio, random

  

async def retry(operation, attempts=5, base_delay=1.0):

for i in range(1, attempts+1):

try:

return await operation()

except Exception as e:

if i == attempts:

raise

delay = base_delay * (2 ** (i-1)) + random.uniform(0, 0.5)

await asyncio.sleep(delay)

```

  

```python

# Batch runner generico

async def run_in_batches(tasks, batch_size):

results = []

for i in range(0, len(tasks), batch_size):

chunk = tasks[i:i+batch_size]

results.extend(await asyncio.gather(*chunk))

return results

```

  

```python

# Conversione flessibile ID policy -> CSV

from typing import Union, List

  

def policies_to_csv(policies: Union[str, List[int], List[str]]) -> str:

if isinstance(policies, str):

return policies

return ",".join(str(p) for p in policies)

```

  

---

  

### Troubleshooting

  

- 401/403 su `/v0/auth/me`: verificare che il backend emetta `session_id` su `/v0/auth/login` e che i cookie siano abilitati (`aiohttp.CookieJar(unsafe=True)`).

- 422 su upload: controllare nomi dei campi multipart e che i file siano PDF validi. Si usa `secure_filename` per evitare problemi di encoding.

- Nessuna sessione su DB durante polling: verificare `user_id` (da `/auth/me`) e connessione `DBManager(APISettings().database_url)`.

- Azure OpenAI errori 429/5xx: aumentare backoff, ridurre `concurrency_prompts`, controllare quota.

  

---

  

### Licenze e Sicurezza

  

- I documenti PDF possono contenere dati sensibili: assicurarsi di rispettare policy aziendali.

- Le chiavi Azure OpenAI vanno tenute fuori repo (usare `.env` non committato).

  

---

  #AI 