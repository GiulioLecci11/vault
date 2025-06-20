## FastAPI: Setup iniziale e struttura base

Per iniziare con FastAPI, bisogna creare un'istanza dell'applicazione con `app = FastAPI()`. Per far partire l'applicazione è necessario importare uvicorn, che è il server ASGI ottimizzato per gestire le applicazioni asincrone.

### Comandi Fondamentali

1. **Creare l'app**: `app = FastAPI()`
2. **Creare le rotte**: `@app.get("/rotta")`
3. **Associare metodi alle rotte**: I parametri sono quelli ottenuti dall'URL (GET) o dal BODY (POST)
4. **Avviare il server**: `uvicorn <filename_python>:<nome_variabile_fastapi> --reload`
5. **Documentazione automatica**: Accessibile su `url/docs` (Swagger UI) o `url/redoc` (ReDoc)

### Metodi HTTP e Comportamenti

- **GET**: Accessibili via URL (es. `/post?key=value`). NON usarle per inviare parametri - non è una buona pratica, usa POST invece
- **POST**: Accesso tramite Postman, curl o Swagger
- **PUT**: Crea una risorsa se non esiste, altrimenti la aggiorna completamente

Esempio di richiesta POST con curl:

```bash
curl -X POST "http://localhost:8000/post" \
     -H "Content-Type: application/json" \
     -d '{"title": "My Post", "body": "This is the content"}'
```

## Programmazione asincrona in FastAPI

Il backend moderno si sviluppa sempre in modalità asincrona. FastAPI e uvicorn sono specificamente ottimizzati per questo paradigma.

### Funzioni Sincrone vs Asincrone

Le funzioni associate ai decoratori possono essere:

**ASINCRONE (Coroutine)**:

- Il server può rispondere ad altre richieste mentre aspetta la risposta
- Python ha il GIL che permette ad un solo thread alla volta di girare

```python
@app.get("/external")
async def get_data():
    response = await httpx.get("https://api.example.com")
    return response.json()
```

**SINCRONE**:

- Il server esegue queste funzioni in thread separati (gestione risorse/tempi subottimale)

```python
@app.get("/simple")
def simple_math():
    return {"result": 2 + 2}
```

### Come FastAPI Gestisce Sync vs Async

**ATTENZIONE - Concetto Fondamentale**:

- **Rotte SINCRONE**: Il server le esegue in thread separati (non bloccanti ma meno efficienti)
- **Rotte ASINCRONE**: Il server le esegue nel thread principale e resta fermo finché non finisce la funzione. È compito della funzione lasciare il controllo al server nei momenti di attesa (identificati dalla keyword `await`)

Se l'endpoint è una coroutine (`async def`) eseguirà il codice direttamente usando `await`, altrimenti eseguirà l'endpoint in un thread separato.

### Matrice di Comportamenti

|Tipo Endpoint|Operazioni Interne|Risultato|Status|
|---|---|---|---|
|`async def`|Operazioni sync (bloccanti)|**Bloccante**|❌|
|`def`|Operazioni sync (bloccanti)|**Non bloccante**|✅|
|`async def`|Operazioni async (con await)|**Non bloccante**|✅|
|`def`|Operazioni async|**Non bloccante**|✅|

**Esempio Pratico Completo**:

```python
import time
import asyncio
from fastapi import FastAPI
import uvicorn

app = FastAPI()

# FastAPI eseguirà ogni chiamata in un thread separato.
# Il time.sleep bloccherà il thread, ma non l'intero server (processo)
# Possiamo far "dormire" (fare operazioni) su più thread contemporaneamente
# ✅ 😎
@app.get("/test-sync")
def test_sync_with_sync_code(sleep_time: int):
    time.sleep(sleep_time)  # Non bloccante solo perché GIL rilascia le risorse per operazioni I/O o sleep
    return {"message": f"Slept for {sleep_time} seconds", "type": "sync"}

# FastAPI farà await su ogni chiamata a questo endpoint e si aspetta che noi
# "gestiamo" il codice async (ovvero chiamando codice bloccante con "await").
# In questo caso il sleep bloccherà il processo dove gira FastAPI,
# le altre richieste dovranno aspettare che questa finisca.
# ❌ 😭
@app.get("/test-async")
async def test_async_with_sync_code(sleep_time: int):
    time.sleep(sleep_time)
    return {"message": f"Slept for {sleep_time} seconds", "type": "async-with-sync-code"}

# FastAPI farà await su ogni chiamata a questo endpoint e si aspetta che noi
# "gestiamo" il codice async (ovvero chiamando codice bloccante con "await").
# In questo caso usiamo una funzione sleep asincrona e facciamo await su di essa.
# Quando facciamo await restituiamo il controllo a FastAPI così altre richieste possono essere gestite.
# ✅ 😎
@app.get("/test-async-with-async-code")
async def test_async_with_async_code(sleep_time: int):
    await asyncio.sleep(sleep_time)
    return {"message": f"Slept for {sleep_time} seconds", "type": "async-with-async-code"}

# Deep Dive
# Python ha il GIL che significa che solo un thread può girare alla volta, quindi come funziona il primo esempio?
# Viene usato uno scheduler per passare da un thread all'altro.
# Come fa il thread a sapere quando cambiare? E come è possibile che più thread dormano (facciano cose) contemporaneamente con il GIL?
# Il GIL è progettato per essere rilasciato dalle operazioni I/O e certe estensioni C.
# Questo significa che il GIL viene rilasciato quando il thread è in attesa di I/O, come uno sleep, quindi più thread possono dormire contemporaneamente.
# ✅ 😎

# Quindi perché dovremmo usare async?
# Le coroutine (codice async) sono molto più leggere rispetto ai thread e hanno meno overhead.
# Questo significa che possiamo gestire più richieste con la stessa quantità di risorse.
# Inoltre è più esplicito dove e quando il controllo viene restituito al thread principale.

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

**Script di Test per Verificare i Comportamenti**:

```bash
#!/bin/bash

# Funzione per fare una richiesta e cronometrarla
make_request() {
    local endpoint=$1
    local sleep_time=$2
    local start_time=$(date +%s.%N)
    echo "Starting request to $endpoint at $(date)"
    curl -s "http://localhost:8000/$endpoint?sleep_time=$sleep_time" > /dev/null
    local end_time=$(date +%s.%N)
    local duration=$(echo "$end_time - $start_time" | bc)
    echo "Completed request to $endpoint in ${duration} seconds at $(date)"
    echo "----------------------------------------"
}

# Test chiamate concorrenti all'endpoint sync
# Queste chiamate verranno eseguite in parallelo: tempo totale di esecuzione sarà 3 secondi
echo "Testing concurrent calls to sync endpoint..."
make_request "test-sync" 3 &
make_request "test-sync" 3 &
make_request "test-sync" 3 &
wait
echo -e "\n"

# Test chiamate concorrenti all'endpoint async con codice sync
# Queste chiamate bloccheranno il thread e verranno eseguite sequenzialmente: tempo totale sarà 9 secondi
echo "Testing concurrent calls to async endpoint with sync code..."
make_request "test-async" 3 &
make_request "test-async" 3 &
make_request "test-async" 3 &
wait
echo -e "\n"

# Test chiamate concorrenti all'endpoint async con codice async
# Queste chiamate verranno eseguite in parallelo: tempo totale di esecuzione sarà 3 secondi
echo "Testing concurrent calls to async endpoint with async code..."
make_request "test-async-with-async-code" 3 &
make_request "test-async-with-async-code" 3 &
make_request "test-async-with-async-code" 3 &
wait
```

## Gestione dei parametri

FastAPI offre tre modalità principali per gestire i parametri delle richieste:

### 1. Parametri di Rotta (Path Parameters)

Si specificano direttamente nel percorso dell'URL racchiudendoli tra parentesi graffe. È fondamentale sempre specificare i type hints per la validazione automatica.

```python
@app.get("/{nome}")
async def get_name(nome: str):   
    # nome verrà preso dall'url e diventa parametro
    return nome
```

### 2. Query Parameters

Parametri che si passano dopo il simbolo `?` alla fine della rotta, utilizzati per filtrare o modificare il comportamento della richiesta.

### 3. Body Parameters

Dati inviati nel corpo della richiesta HTTP, definiti come modelli Pydantic:

```python
@app.post("/post")
async def create_dict(content: dict):
    my_dict.append(content)
    return my_dict	
```

Per situazioni specifiche, si può utilizzare `Annotated` per definire esplicitamente se un parametro appartiene agli header, al body o ad altre parti della richiesta.

## Utilizzo di Pydantic

### Validazione dei Tipi

**Valori di INPUT**:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()
posts = []

class Post(BaseModel):
    id: int
    body: str

@app.post("/post")
async def create_post(content: Post):
    posts.append(content)
    return posts
```

**Valori di OUTPUT**:

```python
@app.post("/post", response_model=list[Post])
async def create_post(content):
    posts.append(content)
    return posts
```

### Valori di Esempio e Validazione

```python
from pydantic import Field

class Post(BaseModel):
    id: str = Field(..., examples=[1])
    body: str = Field(..., examples=["Contenuto del dizionario"])

# I "..." sono un finto valore di default - se il tipo non corrisponde solleva eccezione
# Si possono aggiungere vincoli:
# Field(..., gt=5, examples=[1])
# Field(..., max_length=10, examples=["Contenuto"])
```

## Pattern REST

Il pattern più diffuso per organizzare le API è REST (Representational State Transfer). La filosofia REST considera tutto ciò che sta nel backend come una "risorsa" a cui si può accedere in lettura o scrittura. Una regola fondamentale è utilizzare sempre **nomi (sostantivi) nei percorsi delle route, mai verbi**, poiché il verbo è già espresso dal metodo HTTP utilizzato.

## Struttura scalabile del progetto

### Organizzazione delle Directory

Per mantenere il codice ordinato e scalabile:

```
fullstack-skeleton/
│
├── backend/                 # FastAPI backend application
│   ├── app/
│   │   ├── routers/        # API route handlers
│   │   ├── models.py       # Database models
│   │   ├── schemas.py      # Pydantic schemas
│   │   ├── auth.py         # Authentication utilities
│   │   ├── database.py     # Database configuration
│   │   └── main.py         # FastAPI app instance
│   ├── common/
│   │   └── dependencies/   # Dependency injection
│   ├── requirements.txt    # Dependencies
│   └── .env               # Environment variables
```

### Routing (Contenitori di Rotte)

1. **Creare router per area tematica**: `router.py` per ogni funzionalità
2. **Definire APIRouter**: Con prefisso comune
3. **Modificare decoratori**: `@router.get("rotta")`
4. **Importare nel main**: E includere nell'app

```python
# router.py
from fastapi import APIRouter
router = APIRouter(prefix="/blog", tags=['Blog'])

@router.post("/post", response_model=list[Post])
async def create_post(content):
    posts.append(content)
    return posts 

# main.py
from src.blog.router import router as blog_router
app.include_router(blog_router)
```

### Requirements

Generare automaticamente con:

```bash
pip freeze > requirements.txt
```

## Dependency Injection e Dependencies

FastAPI offre un sistema di dependency injection molto potente attraverso la funzione `Depends()`.

### Organizzazione Best Practices

- Creare una cartella `common/dependencies` dove inserire i "pezzetti di codice" riutilizzabili
- Negli endpoint, richiamare solamente le dependencies necessarie
- Implementare la dependency injection nei file `api.py` delle singole collezioni
- Creare un file separato per ogni macro-funzionalità

## Configurazione e Settings

### Gestione Professionale delle Configurazioni

Per gestire le configurazioni in modo professionale:

- Creare un file `settings.py` importando `BaseSettings` da Pydantic
- Definire tutti i valori di configurazione in un file `.env`
- Questo approccio permette di gestire facilmente diverse configurazioni per sviluppo, test e produzione

```python
from pydantic_settings import BaseSettings

class APISettings(BaseSettings):
    database_url: str
    api_key: str
    
    class Config:
        env_file = ".env"

settings = APISettings()
```

L'approccio con Pydantic BaseSettings garantisce la validazione automatica delle configurazioni e una gestione type-safe delle variabili d'ambiente.

## Database (dbeaver per visualizzare file test.db)

### Approccio CRUD

Tutte le comunicazioni col database sono una dipendenza, ma non le usiamo direttamente dentro le rotte. Si preferisce centralizzare in un unico punto tutte le comunicazioni col db in una classe CRUD.
***Nel file CRUD.PY puoi trovare tanti esempi di query***

### ORM e Modelli

Usare SQLModel (dello stesso sviluppatore di FastAPI) o SQLAlchemy. L'idea è definire i modelli delle tabelle come oggetti Python (tecnica ORM):

```python
class User(DBBaseModel, table=True):
    id: int = Field(default=None, primary_key=True)
```

Esempio di due tabelle complesse e correlate tra loro da una relazione
  
```python

class CommercialConditionCheck(DBBaseModel, table=True):

	__tablename__ = "commercial_condition_check"
	
	id: int = Field(default=None, primary_key=True)
	
	id: int = Field(default=None, primary_key=True)
	
	user_id: int = Field(foreign_key="user.id")
	
	commercial_condition_id: int
	
	commercial_conditions_ids: list[int] = Field(default=[])
	
	check_session_id: int = Field(foreign_key="check_session.id")
	
	overview: str = Field(default="")
	
	document_id: int
	
	commercial_conditions_details: str = Field(default="")
	
	outcome: CheckOutcome
	
	state: ReportState = Field(
	
	reasoning: str
	
	default=ReportState.pending, sa_column=Column(Enum(ReportState))
	
	created_at: datetime = Field(
	default_factory=lambda: datetime.now(timezone.utc),
	)
	
	updated_at: datetime | None = Field(
	default=None, sa_column=Column(DateTime(), onupdate=func.now())
	)
	
	# Relationships
	
	check_session: "CheckSession" = Relationship(
	back_populates="commercial_condition_checks"
	)
```

```python
class CheckSession(DBBaseModel, table=True):

	__tablename__ = "check_session"
	
	id: int = Field(default=None, primary_key=True)
	
	user_id: int = Field(foreign_key="user.id")
	
	commercial_conditions_ids: List[int] = Field(
	
	sa_column=Column(JSON)
	) # Sqlalchemy doesn't support List type
	
	document_ids: List[int] = Field(sa_column=Column(JSON))
	
	state: CheckSessionState = Field(default=CheckSessionState.RUNNING)
	
	created_at: datetime = Field(
	default_factory=lambda: datetime.now(timezone.utc),
	)
	
	updated_at: datetime | None = Field(
	default=None, sa_column=Column(DateTime(), onupdate=func.now())
	)
	
	completed_at: datetime | None = Field(default=None)
	
	running_checks_count: int
	
	compliant_checks_count: int
	
	not_compliant_checks_count: int
	
	not_found_checks_count: int
	
	summary_available_checks_count: int
	
	#Relationships
	
	commercial_condition_checks: List[CommercialConditionCheck] = Relationship(
	back_populates="check_session")

```

## Examples (from ABB Backend)
- Dentro api/routes/v0 fare una rotta diversa per ogni funzionalità diversa del backend (in abb noi abbiamo /policy_check ma anche /commercial_condition per gettare tutte le policy dal db). Spostare le rotte in base al pattern REST FUL. 
  Se ho bisogno di una risorsa diversa (come le commercial conditions, che prescindono dal policy check) faccio una rotta diversa. 
  Una rotta con "" non aggiunge nulla rispetto al path che sta nel prefix (che qui nel codice non mostro). Esempio:
```python
@router.get("")


def get_commercial_conditions(

current_user=Depends(dependencies.authentication_manager.get_current_user),
) -> Dict[str, CommercialConditionDetails]:

	commercial_conditions_details = dependencies.policy_checking_pipeline_manager.get_commercial_conditions_details()

	return commercial_conditions_details
```

- Nell'interagire col db fare ATTENZIONE ALLA SESSION DEL DB STESSO in quanto non si possono lanciare due get_session() di fila (ma questa funzione può essere embeddata in alcune funzioni scritte da Leo come get_check_session() o altre). Risolto con la seguente implementazione dove se una session è già stata definita (esternamente, con un get_session() ) la passiamo come parametro 'current_session' a get_check_session()
```python
def get_check_session(self, user_id: int, session_id: int, current_session = None) -> CheckSession:
        if current_session is not None:
            check_session = current_session.exec(
                select(CheckSession)
                .where(CheckSession.user_id == user_id)
                .where(CheckSession.id == session_id)
            ).first()
        else:
            with self.db_manager.get_session() as db_session:
                check_session = db_session.exec(
                    select(CheckSession)
                    .where(CheckSession.user_id == user_id)
                    .where(CheckSession.id == session_id)
                ).first()
                if not check_session:
                    raise ResourceNotFoundException(
                        f"Check session with id {session_id} not found"
                    )
        return check_session
```
  Abbiamo pure cambiato il nome della session del db in -> db_session. Mentre check_session si riferisce alla run del policy checker
  
Considerazione su CRUD.py:tutti nomi molto simili per le operazioni del db. Voluto e sono leggermente diverse in quello che fanno, però si riferiscono tutte e 3 alla CheckSession (run del policyChecker) ma al loro interno chiamano pure get_session() che si riferisce alla session del db.

Ecco un esempio di operazioni di scrittura (creazione e update) di una tabella nel db, queste operazioni stanno dentro crud.py. 
Nota che PURTROPPO abbiamo avuto la brillante idea di chiamare una run di policy checker come CheckSession quindi la nomenclatura è un po' infelice.
```python
def create_check_session(
        self,
        user_id: int,
        commercial_conditions_ids: list[int],
        document_ids: list[int],
    ) -> CheckSession:
        with self.db_manager.get_session() as db_session:
            check_session = CheckSession(
                user_id=user_id,
                commercial_conditions_ids=commercial_conditions_ids,
                document_ids=document_ids,
            )
            db_session.add(check_session)
            db_session.commit()
            db_session.refresh(check_session)
            return check_session
        
    def update_check_session_status(
        self,
        session_id: int,
        status: CheckSessionState,
    ) -> CheckSession:
        with self.db_manager.get_session() as db_session:
            
            check_session = self.get_check_session(1, session_id, db_session)
            check_session.state = status
            check_session.completed_at = datetime.now()
            db_session.commit()
            db_session.refresh(check_session)
            return check_session
```

⚠️DA SISTEMARE
### Models.py VS Db Models

I modelli (models.py) posti all'interno delle rotte sono utilizzati per quelle specifiche rotte per esempio per formattare le risposte dell'utente (difatti nel nome presentano spesso finali come "out" o "response"). Questo perché magari nei db_models ho un sacco di dati che non voglio esporre all'utente, i modelli nelle singole rotte potrebbero essere sottoinsiemi o unioni di db_models vari.

### Middleware

Ogni volta che arriva una richiesta viene intercettata dal middleware, si usano per esempio per i logger
```python
from fastapi.middleware.cors import CORSMiddleware


app.add_middleware(
CORSMiddleware,
allow_origins=["*"],
allow_credentials=True,
allow_methods=["*"],
allow_headers=["*"],
)
```
2. Cosa fa backpopulates? Serve ad esplicitare la foreign key non solo al db ma anche a sql model. Funziona anche senza comunque
3. Dove vanno messe le operazioni che sfruttano dependencies.crud e quindi che interagiscono col db? Noi le abbiamo messe dentro la parte di common/dependencies/ai  facendo attenzione a if crud (in and con altra roba) prima di usarle. SI, bravissimi

⚠️FINE PARTE DA SISTEMARE 
### Alembic per le Migration

Una migration è una modifica dello schema del database senza dover riscrivere da zero. Alembic è un framework per questo:

**Comandi principali**:

```bash
# Creare una revisione manuale
alembic revision -m "add info to user"

# Creare una revisione automatica (consigliato)
alembic revision --autogenerate -m "messaggio"

# Applicare le migration
alembic upgrade head
```

Il comando `--autogenerate` genera automaticamente il file di migration basandosi sulle differenze nei modelli.

## Redis (Caching)

Nel nostro caso è utile per fare caching. Tramite la libreria `fastapi-cache` possiamo interagire con il server Redis:

```python
@app.route('/welcome')
@cache(expire=20)
def welcome_message() -> str:
    time.sleep(100)
    return 'Benvenuto!'
```

**Funzionamento**:

- Prima interazione: aspetta 100s e stampa benvenuto
- Successive interazioni: risposta immediata dalla cache
- `expire=20`: cache eliminata ogni 20 secondi

## Celery:
Framework utile per gestire il sincronismo in maniera molto automatizzata. **UTILE QUANDO SI HANNO OPERAZIONI MOLTO RESOURCE DEMANDING** (come train di modelli)

## FastAPI background tasks:
Come Celery, ma utile per operazioni più leggere (https://fastapi.tiangolo.com/tutorial/background-tasks/) 

```python
from fastapi import Depends, BackgroundTasks

@router.post("/policy-checking-pipeline")
    def post_policy_checking_pipeline(
        body: PolicyCheckingPipelineRequest,
        background_tasks: BackgroundTasks,
        policy_checking_pipeline_manager=Depends(
            lambda: dependencies.policy_checking_pipeline_manager
        ),
    ) -> Dict[str, str]:
        """Start the policy checking pipeline in background and return immediately."""
        commercial_conditions_ids = body.commercial_conditions_ids
        analysis_mode = body.analysis_mode
        # Persist a pending Report and retrieve its id
        report_id = dependencies.crud.create_report_record(
            user_id=1,         # TODO: replace user_id placeholder with user info from authentication when available
            commercial_conditions_ids=commercial_conditions_ids,
        )
        # Start the policy checking pipeline in background
        background_tasks.add_task(
            policy_checking_pipeline_manager.run_policy_check,
            commercial_conditions_ids,
            analysis_mode,
        )
        # Immediate response to the client including report id
        return {"message": "Analysis started!", "report_id": report_id}
```
SISTEMARE QUESTA PARTE⚠️
Nota come si vada a passare un parametro fittizio alla rotta (ossia proprio BackgroundTasks) che però da swagger (o in generale nell'utilizzo reale) non dobbiamo passare. Poi dentro la rotta si passa il "metodo" (in questo caso policy_checking_pipeline_manager.run_policy_check) che esegue quella rotta all'interno di background_tasks.add_task come parametro e i parametri del metodo a sua volta come parametri di add_task messi posizionalmente DOPO di lui.

Questo codice ci permette di lanciare il task in background, ma quando il task finisce non andrà in automatico a "sovrascrivere" la risposta analysis started, permette solo di "liberare" l'esecuzione perché è come se lanciassimo una funzione async facendo await. Siamo noi poi a dover prevedere che il codice della dipendenza usata ossia (policy_checking_pipeline_manager.run_policy_check ) infine ci reindirizzi sul risultato dell'analisi

SISTEMARE E AGGIUNGI CHE ci facciamo tornare assieme ad analysis started anche l'id del report che sta per essere generato così da poterlo usare per richiedere esattamente quel report lì

AGGIUNGI PURE CHE polling al db aspettando risultato e quando trovi results=done visualizzi pacchetto a frontend

## File handling through file system:
https://git.datapizza.tech/lab-ai/customers/abb/policy-checker/tc-ai/-/commit/8e5f553ee1f7f9ab144d0902f57ed44a0a77fd8f

## Concorrenza in Python: Thread vs. Asyncio

### Cos'è il GIL?

Il **Global Interpreter Lock (GIL)** è un blocco che permette l'esecuzione del codice Python a un solo thread alla volta. Pensalo come un "bastone della parola" - solo il thread che lo possiede può eseguire codice Python.

- Alcune operazioni (principalmente I/O come lettura file, richieste di rete e `time.sleep()`) rilasciano temporaneamente il GIL, permettendo ad altri thread di essere eseguiti
- Puoi verificare se un'operazione rilascia il GIL:
    - Eseguendo un test semplice con due thread e verificando se vengono eseguiti veramente in parallelo
    - Controllando la documentazione delle librerie (alcune lo menzionano)
    - Cercando le chiamate `Py_BEGIN_ALLOW_THREADS` nel codice C 😨

### Thread

I **Thread** sono come lavoratori separati che condividono la memoria ma possono essere messi in pausa e ripresi dal computer.

- **Come funzionano:** Il sistema operativo crea e gestisce questi lavoratori, decidendo quando ciascuno viene eseguito
- **Parallelismo:** I thread possono lavorare contemporaneamente, ma solo quando eseguono operazioni che rilasciano il GIL
- **Utili per:** Operazioni che attendono molto (come il download di file) o utilizzano estensioni C che rilasciano il GIL
- **Limitazioni:** Il GIL significa che il codice Python puro non può utilizzare più core CPU contemporaneamente
- **Uso delle risorse:** Ogni thread richiede memoria e il passaggio tra thread richiede tempo

```python
from concurrent.futures import ThreadPoolExecutor
import requests

def download_file(url):
    # This waits for the network and releases the GIL
    return requests.get(url).content

# Creates actual separate workers (threads)
with ThreadPoolExecutor(max_workers=10) as executor:
    results = executor.map(download_file, urls)
```

### Asyncio

**Asyncio** è come avere un solo lavoratore che si alterna tra molti compiti ogni volta che un compito deve attendere qualcosa.

- **Come funziona:** Tutto viene eseguito su un singolo thread, e i compiti si mettono volontariamente in pausa quando sono in attesa
- **Concorrenza:** I compiti si alternano nell'esecuzione ma non vengono eseguiti veramente in contemporanea
- **Utile per:** Programmi che attendono molto (come la gestione di molte richieste web)
- **Vantaggi:** Usa pochissima memoria, più prevedibile, nessun problema con il GIL
- **Uso delle risorse:** Molto efficiente - può gestire migliaia di compiti

```python
import asyncio
import aiohttp

async def download_file(url):
    # This pauses the function and lets other tasks run
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.read()

# One worker handling multiple tasks
async def main():
    tasks = [download_file(url) for url in urls]
    results = await asyncio.gather(*tasks)

asyncio.run(main())
```

### Quando Usare Cosa

- **Usa i Thread** quando:
    - Lavori con librerie normali che non supportano asyncio
    - Devi fare poche cose contemporaneamente (fino a decine)
    - Usi librerie con codice C che rilasciano il GIL
- **Usa Asyncio** quando:
    - Gestisci molte connessioni contemporaneamente (centinaia o migliaia)
    - Usi librerie moderne con supporto async (come aiohttp, asyncpg)
    - Hai bisogno dell'uso più efficiente della memoria

### Perché Dovremmo Usare Async?

Le coroutine (codice async) sono molto più leggere rispetto ai thread e hanno meno overhead. Questo significa che possiamo gestire più richieste con la stessa quantità di risorse. Inoltre è più esplicito dove e quando il controllo viene restituito al thread principale.

## Exception Handling Avanzato

### Centralizzazione della Gestione Errori

Per migliorare la manutenibilità e ridurre il carico cognitivo, centralizza la logica delle eccezioni.

### 1. Definire Eccezioni Personalizzate

```python
import traceback

class ProjectBaseException(Exception):
    def __init__(self, message: str):
        super().__init__(message)
        self.error_type = self.__class__.__name__
        self.message = message

    def to_json(self, include_stacktrace: bool = False):
        return {
            "error_type": self.error_type,
            "message": self.message,
        } | ({"stacktrace": traceback.format_exc()} if include_stacktrace else {})

class DatabaseException(ProjectBaseException):
    pass

class ResourceNotFoundException(ProjectBaseException):
    pass
```

### 2. Registrare Gestori Globali

```python
from fastapi import FastAPI, Request, status
from fastapi.responses import JSONResponse

def register_exception_handlers(
    app: FastAPI, include_stacktrace: bool = False
) -> FastAPI:
    @app.exception_handler(DatabaseException)
    def database_exception_handler(request: Request, exc: DatabaseException):
        return JSONResponse(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            content=exc.to_json(include_stacktrace=include_stacktrace),
        )

    @app.exception_handler(ResourceNotFoundException)
    def resource_not_found_exception_handler(
        request: Request, exc: ResourceNotFoundException
    ):
        return JSONResponse(
            status_code=status.HTTP_404_NOT_FOUND,
            content=exc.to_json(include_stacktrace=include_stacktrace),
        )

    # Gestore per TUTTE le eccezioni non gestite
    @app.exception_handler(Exception)
    def general_exception_handler(request: Request, exc: Exception):
        return JSONResponse(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            content={
                "error_type": "InternalServerError",
                "message": "An unexpected error occurred",
                "details": str(exc) if include_stacktrace else None
            }
        )

    return app
```

### 3. Catturare e Rilanciare nel Codice Logico

```python
def get_item_logic(id: int):
    try:
        item = db.get(id)
    except SomeDBError:
        raise DatabaseException(f"Failed retrieving item {id}")
    if item is None:
        raise ResourceNotFoundException(f"item {id} not found")
    return item
```

### 4. Endpoint Puliti

**❌ Prima: gestione manuale degli errori**

```python
@app.get("/items/{id}")
def get_item(id: int):
    try:
        item = db.get(id)
        if item is None:
            raise HTTPException(status_code=404, detail="Item not found")
        return item
    except SomeDBError:
        raise HTTPException(status_code=500, detail="Database error")
```

**✅ Dopo: lascia che gli handler gestiscano**

```python
@app.get("/items/{id}")
def get_item(id: int):
    return get_item_logic(id)
```

### 5. Bonus: Gestione Specifica per Endpoint

Se hai bisogno di eseguire codice specifico dell'endpoint puoi ancora farlo catturando e rilanciando le eccezioni (cerchiamo di minimizzare questo):

```python
@app.get("/items/{id}")
def get_item(id: int):
    try:
        item = get_item_logic(id)
    except ResourceNotFoundException as e:
        # ...codice specifico dell'endpoint...
        logger.warning(f"Item {id} not found for user {current_user}")
        raise e
        
    return item
```

## Endpoint di Streaming

Con lo streaming, se si verifica un'eccezione dopo l'inizio della risposta, è troppo tardi per restituire un errore HTTP. Quindi **FastAPI non invocherà i gestori di eccezioni durante le risposte in streaming**. Il client ha già ricevuto un codice di successo e qualsiasi eccezione solleverà un RuntimeError in FastAPI.

**⚠️ Qualsiasi errore deve essere gestito e restituito come parte dei dati in streaming. Il client deve essere consapevole di questo!**

### Esempio del Problema

```python
@app.get("/stream")
def stream_endpoint():
    def stream_data():
        yield "first chunk"
        raise Exception("something went wrong!")
    return StreamingResponse(stream_data())
```

Il client riceverà un 200 OK e "first chunk," poi... niente o un errore lato client. Ma non saprà che si è verificato un errore lato server.

### Soluzione: Modelli per lo Streaming

Prima definiamo i modelli base per gestire le risposte in streaming:

```python
from abc import ABC, abstractmethod
from pydantic import BaseModel
from typing import Callable, Generator
from functools import wraps
import traceback

class Encodable(ABC):
    # Questo metodo è necessario per utilizzare i modelli Pydantic nelle risposte streaming
    # La risposta streaming chiama encode sull'oggetto ricevuto
    @abstractmethod
    def encode(self, encoding: str = "utf-8", errors: str = "strict") -> bytes:
        pass

class NetworkBaseModel(BaseModel, Encodable):
    def encode(self, encoding: str = "utf-8", errors: str = "strict") -> bytes:
        return (self.model_dump_json() + "\n").encode(encoding, errors)

class StreamingResponseError(NetworkBaseModel):
    # Usato per incapsulare l'errore in un JSON usando .encode()
    error_type: str
    message: str
    stacktrace: str | None = None
```

### Gestione Errori in Streaming

Definiamo un decoratore che avvolge le funzioni generatrici e gestisce le eccezioni:

```python
def handle_streaming_exceptions(
    include_stacktrace: bool = False,
    response_format_adapter: Callable[
        [StreamingResponseError], Encodable
    ] = lambda e: e,
) -> Callable:
    """
    Decoratore che avvolge i generatori streaming per gestire le eccezioni con eleganza.
    """

    def decorator(generator_func: Callable) -> Callable:
        @wraps(generator_func)
        def wrapper(*args, **kwargs) -> Generator[str, None, None]:
            try:
                # Esegue il generatore originale
                for item in generator_func(*args, **kwargs):
                    yield item

            except (
                DatabaseException,
                ResourceNotFoundException,
            ) as e:
                # Gestisce le nostre eccezioni personalizzate
                yield response_format_adapter(
                    StreamingResponseError(
                        **e.to_json(include_stacktrace=include_stacktrace)
                    )
                )
            except Exception as e:
                # Gestisce tutte le altre eccezioni
                yield response_format_adapter(
                    StreamingResponseError(
                        error_type=e.__class__.__name__,
                        message=str(e),
                        stacktrace=traceback.format_exc()
                        if include_stacktrace
                        else None,
                    )
                )

        return wrapper

    return decorator
```

### Utilizzo Pratico

```python
from fastapi.responses import StreamingResponse

@app.get("/stream")
def stream_endpoint():
    @handle_streaming_exceptions(include_stacktrace=True)
    def stream_data():
        for id in item_ids:
            try:
                item = get_item_logic(id)  # Può lanciare DatabaseException o ResourceNotFoundException
                yield NetworkBaseModel.parse_obj({"data": item})
            except Exception as e:
                # Le eccezioni vengono gestite automaticamente dal decoratore
                raise e
                
    return StreamingResponse(stream_data(), media_type="application/json")

# Con adapter personalizzato per formato specifico
@app.get("/stream-custom")
def stream_endpoint_custom():
    def custom_adapter(error: StreamingResponseError) -> Encodable:
        # Adatta il formato dell'errore per questa API specifica
        class CustomError(NetworkBaseModel):
            status: str = "error"
            error_info: dict
        
        return CustomError(error_info=error.dict())
    
    @handle_streaming_exceptions(
        include_stacktrace=False,
        response_format_adapter=custom_adapter
    )
    def stream_data():
        for id in item_ids:
            item = get_item_logic(id)
            yield {"status": "success", "data": item}
                
    return StreamingResponse(stream_data(), media_type="application/json")
```

**Esempio di Risposta al Client**:

Se `db.get_item(id)` lancia un'eccezione, l'errore verrà catturato e inviato come JSON nel flusso:

```json
{"data": {...}}
{"data": {...}}
{"error_type": "DatabaseException", "message": "Failed retrieving item 123", "stacktrace": "..."}
```

**⚠️ Importante**: Il client deve essere programmato per riconoscere e gestire questi messaggi di errore nel flusso di dati!

## Documentazione Automatica

Una delle caratteristiche più potenti di FastAPI è la generazione automatica della documentazione:

- **Swagger UI**: Accessibile su `/docs` - interfaccia più intuitiva per testing
- **ReDoc**: Accessibile su `/redoc` - documentazione più pulita

Per testare le richieste POST è necessario utilizzare questa documentazione automatica, poiché non è possibile eseguirle direttamente dalla barra degli indirizzi del browser.

## Best Practices Riassuntive

1. **Usa async quando possibile** per ottimizzare le performance
2. **Centralizza la gestione degli errori** con exception handlers
3. **Organizza il progetto** con router separati per funzionalità
4. **Usa Pydantic** per validazione input/output automatica
5. **Segui il pattern REST** con sostantivi nelle route
6. **Gestisci le configurazioni** con BaseSettings e variabili d'ambiente
7. **Implementa dependency injection** per codice modulare e testabile

## Ulteriori Risorse e Approfondimenti

### Performance Analysis

- [Analisi delle prestazioni async vs sync in FastAPI](https://thedkpatel.medium.com/fastapi-performance-showdown-sync-vs-async-which-is-better-77188d5b1e3a) - Suggerimento: usa async quando puoi
- [Risposta dettagliata su Stack Overflow sul funzionamento interno di FastAPI](https://stackoverflow.com/questions/71516140/fastapi-runs-api-calls-in-serial-instead-of-parallel-fashion/71517830#71517830) con esempi pratici

### Filosofia dell'Exception Handling

**$\text{Centralizza la gestione delle Eccezioni.}$**

La gestione delle eccezioni può diventare rapidamente complessa. Alcuni problemi con il codice di gestione degli errori tradizionale:

- I blocchi try/except sparsi e profondamente annidati aumentano la complessità cognitiva
- Codice di gestione errori e risposte agli errori duplicate
- È necessario sapere quali eccezioni una funzione potrebbe sollevare (aumenta il carico cognitivo)
- Porta facilmente a mischiare logica e codice di rete

In FastAPI, puoi semplificare questo _**centralizzando**_ la logica delle eccezioni. Questo migliora la manutenibilità e riduce il carico cognitivo per gli sviluppatori.

Non dovresti catturare eccezioni non gestite: se succede qualcosa di grave vuoi che la tua app fallisca così da poterla aggiustare.

> 🦇: E perché cadiamo, Bruce? Per imparare a rialzarci.

### Approccio al Mapping delle Eccezioni

Il processo è il seguente:

1. Capisco quali sono le eccezioni della classe che andrò a wrappare
2. Catturo quelle eccezioni e genero le mie
3. Per ogni mia eccezione creo un gestore
4. Ora, ogni volta che il sistema sottostante cambia, aggiorno il mapping: $\text{External class exception}\rightarrow \text{My exception with its handler}$


[[Fast & Rest API + Snowflake]]
#miscellaneous 