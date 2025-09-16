## Cos'è un endpoint?

Un **endpoint** è il punto di contatto tra il frontend e il backend. È formato da:

- **Rotta**: il percorso dopo il dominio (`/users`, `/products/123`)
- **Metodo HTTP**: `GET`, `POST`, `PUT`, `DELETE`, ecc.

```
<https://miodominio.com:8000/users/123>
                          ^^^^^^^^^^^
                          questa è la rotta
```

## Creare un endpoint semplice

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
def saluta() -> str:
    return "Hello World!"

```

**Risultato**: Andando su `http://localhost:8000/hello` vedrai "Hello World!"

> Tip: Organizza sempre le rotte seguendo il pattern [Rest API](https://www.notion.so/Rest-API-24d04d204fa280a0ae3ef829a55d94a2?pvs=21)

## Perché usare APIRouter?

**Problema**: Se metti tutti gli endpoint in `app.py`, il file diventa enorme e disorganizzato.

**Soluzione**: Usa `APIRouter` per:

- Raggruppare endpoint correlati
- Mantenere il codice ordinato
- Riutilizzare logiche comuni

## Organizzazione con APIRouter

### 1. Crea un router per risorsa

```python
# routes/users.py
from fastapi import APIRouter
from models import User

def create_users_router() -> APIRouter:
    router = APIRouter(prefix="/users")

    @router.get("/{user_id}")
    def get_user(user_id: int) -> User:
        # Logica per ottenere utente
        return User(id=user_id, name="Mario")

    @router.post("")
    def create_user(user: User) -> User:
        # Logica per creare utente
        return user

    return router

```

**Questo crea automaticamente**:

- `GET /users/{user_id}` - Ottieni utente
- `POST /users` - Crea utente

### 2. Registra il router nell'app principale

```python
# app.py
from fastapi import FastAPI
from routes.users import create_users_router

app = FastAPI()

# Registra tutti i router
app.include_router(create_users_router())

```

## Struttura consigliata

```
project/
├── app.py                 # App principale
├── routes/
│   ├── users.py          # Endpoint utenti
│   ├── products.py       # Endpoint prodotti
│   └── auth.py           # Endpoint autenticazione
└── models/
    └── __init__.py

```

## Pattern comuni

### Versioning API

```python
# Per API v1
router = APIRouter(prefix="/v1/users")

# Per API v2
router = APIRouter(prefix="/v2/users")

```

### Raggruppamento per funzionalità

```python
# Auth
auth_router = APIRouter(prefix="/auth")

# Admin
admin_router = APIRouter(prefix="/admin")

# Public
public_router = APIRouter(prefix="/public")

```

#dpKnowledge 