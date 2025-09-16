Per scrivere i Backend in python usiamo FastAPI. In ordine:

1. Crei l'app con `app = FastAPI()`
    
2. Crei le rotte con `@app.get("/rotta")`
    
3. Associ metodo alle rotte. I relativi parametri sono quelli che ottieni dall'URL (get) o dal BODY (post)
    
4. Avvii il server con `uvicorn <filename python>:<nome variabile fastapi> --reload`
    
5. (OPTIONAL) Se vuoi vedere una documentazione generata automaticamente, puoi farlo con swagger andando su `url/docs`
    
6. Le rotte con:
    
    - **GET**: sono accessibili via URL (es. `/post?key=value`). NON USARLE PER INVIARE PARAMETRI (non è una buona pratica, piuttosto usa post se devi inviare parametri)
    - **POST**: devi accederci con postman o curl o swagger PER INVIARE PARAMETRI, es.
    
    ```bash
    curl -X POST "<http://localhost:8000/post>" \\\\
    	 -H "Content-Type: application/json" \\\\
    	 -d '{"title": "My Post", "body": "This is the content"}'
    
    ```
    

`uvicorn` è il server che gestisce le richieste.

## Caratteristiche

- Le funzioni associate ai decoratori (quelli che "catturano" le rotte) possono essere:
    
    - **ASINCRONE**: il server può rispondere ad altre richieste mentre aspetta la risposta. NB in python c'è il [GIL](https://www.notion.so/GIL-24d04d204fa28066829cee1e1eb4044f?pvs=21) che permette ad un solo thread alla volta di girare.
        
        ```python
          @app.get("/external")
          async def get_data():
          	response = await httpx.get("<https://api.example.com>")
          	return response.json()
        
        ```
        
    - **SINCRONE**: il server resta fermo finché non finisce questa funzione.
        
        ```python
        @app.get("/simple")
        def simple_math():
        	return {"result": 2 + 2}
        
        ```
        
- Puoi associare delle **rotte dinamiche** ai metodi usando le parantesi graffe. i.e.
    

```python
@app.get("/{nome}")
asynce get_name(nome):
	# -> nome verrà preso dall'url e diventa parametro
	return nome

```

- Puoi avere il decoratore con:
    - GET: hai i parametri passati nell'url (NON FARLO, usa post per i parametri)
        
    - POST: hai i parametri passati nel body
        
        ```python
        @app.post("/post")
        async def create_dict(content:dict)
        	my_dict.append(content)
        	return my_dict
        ```
        

## Utilizzo di Pydantic

### Validazione dei tipi

La validazione dei tipi con Pydantic si fa su:

- valori di **INPUT**:

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

- valori di **OUTPUT**:

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()
posts = []

class Post(BaseModel):
    id: int
    body: str

@app.post("/post",response_model=list[Post])
async def create_post(content):
    posts.append(content)
    return posts

```

### Valori di esempio

Se voglio assegnare ai miei valori dei valori di esempio, posso farlo con Pydantic.

```python
...
from pydantic import Field
...

class Post(BaseModel):
    id: str = Field(...,examples=[1])
    body: str = Field(...,examples=["Contenuto del dizionario"])
# da un esempio di input che è utile per debugging della funzione

# NB. i "..." sono un finto valore di default, ovvero che se title non è str allora solleva un'eccezione
...

```

Nei Field posso anche mettere dei vincoli, tipo `Field(...,gt=5,examples=[1])`

`Field(...,max_length=10,examples=["Contenuto del dizionario"])`

## Struttura standard di progetto

### Requirements

1. Li genero in automatico con `pip freeze > requirements.txt`

### Routing in FastAPI (contenitori di rotte)

1. Creo per area tematica `router.py` in cui inserisco tutte le rotte.
    
2. Dentro `router.py` creo un `APIRouter` a cui posso assegnare un prefisso comune.
    
3. Modifico i decoratore con `@router.get("rotta")`.
    
4. Nella classe main, vado ad importare tutti i file router che ho definito con `from {path}.router import router as blog_router`
    
5. Assegno all'app (variabile FastAPI) i router che ho importato con `app.include_router(blog_router)`
    
    ```python
     # router.py
     from fastapi import APIRouter
     router = APIRouter(prefix="/blog",tags=['Blog'])
     ...
     @router.post("/post",response_model=list[Post])
     async def create_post(content):
         posts.append(content)
         return posts
    
     # main.py
     ...
     from src.blog.router import router as blog_router
     app.include_router(blog_router)\\\\
     ...
    
    ```
    

### Models (database)

Ci sono:

- `models.py`: contiene la definizione delle tabelle da usare nel database. Tipicamente interagiamo usando **SQLAlchemy** che fa da ponte in modo da eseguire le query sql via python.
- `database.py`: si occupa di creare la connessione e le operazioni con il database

Una buona pratica è inserire tutti i dati nel database piuttosto che tenerli in un file `.py` così una volta che va in deploy, per modificare quei file basta modificare il db e fare polling rispetto che rifare il deploy post modifica sul file `.py`.

### Redis (caching)

Nel nostro caso ritorna utile per fare caching. Abbiamo un'architettura client/server in cui ogni client stora nel server la cache. Tramite la libreria `fastapi-cache` possiamo interagire con il nostro server redis.

Esempio

```python
@app.route('/welcome')
@cache(expire=20)
def welcome_message()->str:
	time.sleep(100)
	return 'Benvenuto!'

```

Dettagli:

- Alla prima interazione aspetta `100s` e poi stampa benvenuto.
    
- Dalla seconda interazione la risposta è immediata perchè Redis cacha l'output della funzione e lo mostra all'istante.
    
    - Per vedere cosa ha cachato, dentro l'app di redis digita il comando:
        
        ```bash
          KEYS *
        
        ```
        
- con `@cache(expire=20)` definisco che ogni `20` secondi la cache viene eliminata.
    
- Ha senso mettere il timeout alto per valori che cambiano di rado e sono sempre gli stessi.
    

### Esempio

```
fullstack-skeleton/
│
├── frontend/                 # Next.js frontend application
│   ├── src/
│   │   ├── app/             # App router pages
│   │   ├── components/      # Reusable components
│   │   ├── contexts/        # React contexts (Auth)
│   │   └── lib/            # Utilities and API client
│   ├── Dockerfile
│   └── package.json
│
├── backend/                 # FastAPI backend application
│   ├── app/
│   │   ├── routers/        # API route handlers
│   │   ├── models.py       # Database models
│   │   ├── schemas.py      # Pydantic schemas
│   │   ├── auth.py         # Authentication utilities
│   │   ├── database.py     # Database configuration
│   │   └── main.py         # FastAPI app instance
│   ├── Dockerfile
│   ├── pyproject.toml      # Python dependencies and configuration
│   └── uv.lock            # Locked dependencies for reproducible builds
│
├── database/
│   └── init/               # Database initialization scripts
├── docker-compose.yml      # Multi-service orchestration
└── README.md

```

#dpKnowledge 