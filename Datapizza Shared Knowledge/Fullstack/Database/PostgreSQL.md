PostgreSQL è un sistema di gestione di basi di dati relazionali (RDBMS), open source, estensibile e conforme agli standard SQL. Supporta anche dati non relazionali come JSON e array.

Lo tiri su dopo che hai fatto i check in locale con SQLite, gira in un container Docker .

---

## Architettura di Base

- **Client-Server**: il client invia comandi SQL, il server li interpreta ed esegue.
- **Database Cluster**: insieme di database gestiti da un singolo server PostgreSQL.
- **Schema**: contenitore logico di tabelle, viste, funzioni, ecc.
- **Tabelle**: struttura principale per la memorizzazione dei dati.
- **MVCC** (Multiversion Concurrency Control): garantisce concorrenza senza lock aggressivi.

---

## Tipi di Dato Supportati

- **Scalari**: `INTEGER`, `TEXT`, `BOOLEAN`, `TIMESTAMP`, `FLOAT`, ecc.
- **Composti**: `ARRAY`, `JSON`, `JSONB`, `HSTORE`, `UUID`, `ENUM`
- **Custom**: supporta la definizione di tipi di dato personalizzati

---

## Esempio di funzionamento

1. Per creare/runnare da Docker l'immagine, esegui nel terminale di docker il comando

```bash
docker run --name tcaidb \\\\
-e POSTGRES_USER=tcai \\\\
-e POSTGRES_PASSWORD=tcaipass \\\\
-e POSTGRES_DB=tcaidb \\\\
-p 5432:5432 \\\\
-d postgres:17
```

1. Nell' `.env` inserisci

```bash
DATABASE_URL = "postgresql+psycopg://tcai:tcaipass@localhost:5432/tcaidb"
```

1. Nel terminale del progetto

```bash
uv add "psycopg[binary]"
```

1. Usi DBeaver come interfaccia grafica per visualizzare il db, come per SQLite

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