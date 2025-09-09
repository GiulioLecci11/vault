SQLite è un database relazionale leggero e self-contained, che salva tutti i dati all'interno di un singolo file. È ideale per applicazioni semplici, sviluppo locale, o ambienti in cui non è necessario un database server esterno come PostgreSQL o MySQL.

Ti salvi in locale un file `.db` che "simula" un database locale con un file e puoi fare le operazioni tramite SQLAlchemy. Successivamente in prod, il db si mette su Docker

Con FastAPI, è comune usare SQLAlchemy come ORM per gestire i modelli e Alembic per le migrazioni del database. Anche se SQLite ha alcune limitazioni (come supporto limitato per ALTER TABLE), funziona bene per la maggior parte degli usi di base.

Struttura del progetto:

```
project/
├── app/
│   ├── main.py
│   ├── models.py
│   └── database.py
├── alembic/
│   ├── versions/
│   └── env.py
├── alembic.ini
└── requirements.txt

```

- `requirements.txt`

```python
fastapi
uvicorn
sqlalchemy
alembic

```

- `app/database.py`

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

```

- `app/models.py`

```python
from sqlalchemy import Column, Integer, String
from .database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    email = Column(String, unique=True, index=True)

```

- `app/main.py`

```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from . import models, database

models.Base.metadata.create_all(bind=database.engine)

app = FastAPI()

def get_db():
    db = database.SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/")
def read_users(db: Session = Depends(get_db)):
    return db.query(models.User).all()

```

### Configurazione Alembic

- Inizia Alembic con: `alembic init alembic`
- In `alembic.ini`, cambia la stringa di connessione:`sqlalchemy.url = sqlite:///./test.db`
- In `alembic/env.py`, modifica la parte in cui si importa Base:

```python
from app.database import Base
from app import models
target_metadata = Base.metadata

```

- Crea una migrazione automatica: `alembic revision --autogenerate -m "create users table"`
- Applica la migrazione: `alembic upgrade head`

A questo punto, puoi avviare FastAPI

SQLite creerà il file `test.db` e Alembic terrà traccia delle modifiche di schema nella cartella alembic/versions.

NOTE:

- SQLite ha limiti nel modificare colonne esistenti: alcune migrazioni potrebbero richiedere workaround.
- Per progetti più grandi o in produzione, usa PostgreSQL o MySQL.

---

### **Quando usare PostgreSQL**

- Hai bisogno di **scalabilità, affidabilità e sicurezza**
- Progetto multiutente con **accessi concorrenti**
- Query complesse, transazioni, **dati relazionali e JSON**
- Applicazioni web, microservizi, analisi dati

### **Quando usare SQLite**

- Progetto **single-user**, senza accessi simultanei
- Vuoi **evitare un server** e semplificare la distribuzione
- App mobile, app desktop, **test/prototipi**, file temporanei
- Database piccolo o temporaneo (fino a qualche GB)

#dpKnowledge 