TLDR:

> In dependencies vai a definire le funzioni che il tuo backend dovrà svolgere (es. AI) e poi richiami queste funzioni all'interno delle rotte.

# Cos'è la Dependency Injection in FastAPI?

La **dependency injection** in FastAPI permette di separare la logica di business (es. accesso al database, autenticazione, servizi esterni) dal codice delle rotte. Questo rende il codice più modulare, testabile e riutilizzabile.

Le dipendenze vengono definite come funzioni o classi e poi "iniettate" nelle rotte tramite il sistema di dependency injection di FastAPI (usando il costrutto `Depends`).

Un'alternativa è definire una classe Dependency che si occupa di gestire la dipendenza e poi richiamarla nelle rotte.

**Esempio semplice:**

```python
from fastapi import Depends, FastAPI

def get_db():
    db = create_db_session()
    try:
        yield db
    finally:
        db.close()

app = FastAPI()

@app.get("/items/")
def read_items(db=Depends(get_db)):
    return db.query(Item).all()

```

In questo esempio, la funzione `get_db` viene iniettata come dipendenza nella rotta `read_items`. FastAPI si occupa di gestire la creazione e chiusura della sessione al database.

**Vantaggi:**

- Separazione delle responsabilità
- Facilità di testing (puoi sostituire le dipendenze nei test)
- Maggiore riusabilità del codice

---

# Struttura delle Cartelle

```python
backend/
├── README.md
├── pyproject.toml
├── Dockerfile
├── .env                                 # NON COMMITTARE
├── ...file di configurazione
│
├── src/
│   └── api/
│       ├── main.py
│       ├── app.py                       # Codice di creazione dell'applicazione
│       ├── settings.py                  # Configurazione dell'applicazione
│       ├── dependencies.py              # Dependency injection globale
│       │
│       ├── common/                      # Componenti condivisi
│       │   ├── utils.py                 # Funzioni di utilità, se sono molte puoi trasformare questa in una cartella con sottomoduli
│       │   ├── models/                  # Modelli dati
│       │   │   ├── db_models.py         # Modelli per il database
│       │   │   └── network_models.py    # Modelli per richieste/risposte API
│       │   └── dependencies/            # Moduli di dipendenza
│       │       ├── auth_manager.py      # Logica di autenticazione
│       │       ├── crud.py              # Operazioni sul database
│       │       ├── db_manager.py        # Gestione connessione al database
│       │       └── ...                  # Altri moduli
│       │
│       ├── routes/                      # Definizione delle rotte API
│       │   └── v0/                      # Versione 0 delle API
│       │       ├── constants.py         # Costanti delle rotte
│       │       ├── auth/                # Endpoint di autenticazione
│       │       │   └── api.py
│       │       ├── route_name_1/
│       │       │   ├── api.py           # Handler delle rotte
│       │       │   └── models.py        # Modelli specifici della rotta
│       │       └── route_name_2/...
│       │
│       └── alembic/...                  # Migrazioni del database
│
└── tests/...

```

`Livello superiore`

Qui vanno tutti i file di configurazione: .env, Dockerfile, pyproject.toml, pytest.ini, alembic.ini e altri…

`src/`

Qui si trova tutto il codice del sistema. **Se hai più servizi, come ad esempio un task runner (celery), che condividono parte del codice puoi aggiungerli qui come `src/tasks`**. Da qui in poi ci concentriamo solo sulla parte `api`.

`src/api/common`

Qui vanno tutti i componenti condivisi, come utility, modelli dati e dipendenze comuni.

`src/api/routes`

Qui si trova il codice relativo alle rotte. Ad esempio, ogni sottocartella può rappresentare una specifica funzionalità o gruppo di endpoint.

`src/api/alembic`

Qui si trovano i file per la gestione delle migrazioni del database.

#dpKnowledge 