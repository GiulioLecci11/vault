Alembic è uno strumento di migrazione del database per Python, usato comunemente insieme a SQLAlchemy, che è l'ORM (Object Relational Mapper) più diffuso in ambito Python.

## A cosa serve Alembic?

Alembic ti permette di gestire l'evoluzione dello SCHEMA di un database nel tempo. In pratica, quando modifichi i modelli del tuo database (ad esempio aggiungi una colonna o una tabella), Alembic può:

- generare script di migrazione (automaticamente o manualmente),
- applicare questi script al database,
- tenere traccia della versione dello schema del database.

---

## Esempio pratico:

Supponi di avere un'app FastAPI con SQLAlchemy, e inizialmente un modello User con solo id e name. Dopo un po' vuoi aggiungere l’email. Senza Alembic, dovresti scrivere a mano il comando SQL per modificare la tabella.

Con Alembic puoi fare:

1. Crea la nuova classe python del db
2. Crei la revisione con le nuove modifiche. Hai due possibilità:
    1. Generarla in automatico (utile quando cambia STRUTTURA):
        
        ```bash
        # crea il db con quanto rappresentato in models
        alembic revision --autogenerate -m "Aggiunta colonna email a User"
        ```
        
    2. Generarla a mano (utile quando devi popolare con dei DATI)
        
3. Invia la revisione e alembic aggiorna il DB:

```bash
alembic upgrade head # salva un eventuale modifica fatta a models

# ALTERNATIVA:
alembic upgade <id_revision>
```

1. Definisci le funzioni CRUD (`get,put`) per aggiornare la nuova classe sul DB

Se vuoi aggiungere dei dati, devi creare una nuova revision in cui a mano fai `INSERT INTO`

Componenti chiave:

- `alembic.ini` – file di configurazione.
- `versions/` – directory che contiene tutti gli script di migrazione.
- `env.py` – script che collega Alembic alla tua app e a SQLAlchemy.

Con `downgrade()`puoi annullare le modifiche fatte nell'`upgrade()`e spostare il puntatore alla penultima revisione ma è importante esplicitare tutto il codice di `undo` dentro `downgrade`.

### Aggiornare i prompt a DB in prod

1. Modifica sul db in prod i prompts
2. Nella tua storia di revisions aggiorna `upgrade()`dell'ultima revisione con il prompt modificato
3. Quando poi farà una modifica e deployerai, allora anche la nuova revisione sarà deployata

### IMPORTANTE

Il sincronismo è nella tabella `alembic_version` in cui c'è l'ultima versione di alembic che è stata eseguita. Se da errore di version non trovata, è perché nella tabella `alembic_version`, lui punta ad un'altra version che non è l'ultima che hai tu, quindi va sostituita.

### Hints

- se ho un campo nuovo (che è NOT NULL) che va aggiunto con una revision e ci sono già presenti dei record nel db, allora quando crei la revision usa `server_default`, i.e.

```python
op.add_column(
        "check_session",
        sa.Column(
            "session_name",
            sqlmodel.sql.sqltypes.AutoString(length=100),
            nullable=False,
            # -> aggiunge la colonna anche nei vecchi records
            server_default="Unnamed session",
        ),
    )

```

#dpKnowledge 