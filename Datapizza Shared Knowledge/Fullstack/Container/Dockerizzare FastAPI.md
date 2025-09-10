### Perché dockerizzare FastAPI?

Docker è essenziale per le applicazioni FastAPI in production per:

- **Consistenza dell'ambiente**: Lo stesso codice gira identico in sviluppo, test e produzione
- **Isolamento delle dipendenze**: Ogni applicazione ha le sue librerie senza conflitti
- **Scalabilità**: Facile deploy su cloud (AWS, GCP, Azure) e orchestrazione (Kubernetes)
- **Portabilità**: L'applicazione gira ovunque ci sia Docker

### Processo di containerizzazione

1. Crei il `Dockerfile`
2. Crei un `.env` personalizzato per quel container
3. Creo il container con il comando nella directory del Dockerfile

```bash
docker build --no-cache \\\\
--secret id=netrc_conf,src=$HOME/.netrc \\\\
--secret id=env_file,src=tc-ai.env \\\\ -t tc-ai:latest .
```

1. La prima volta lancio il container con il comando

```bash
docker run --rm \\\\
  --env-file tc-ai.env \\\\
  --mount type=bind,source=/Users/lorenzozanolin/.netrc,target=/root/.netrc,readonly \\\\
  -p 8000:8000 \\\\
  tc-ai:latest
```

Poi basta usare play di Docker Desktop perché è tutto cachato

### Esempio di Dockerfile per FastAPI

```docker
FROM python:3.11-slim

WORKDIR /app

# Copia requirements e installa dipendenze
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copia il codice dell'applicazione
COPY ./app ./app

# Espone la porta 8000
EXPOSE 8000

# Comando per avviare l'applicazione
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Gestione dei file con Docker

**Importante**: Se usi un file handler (come nell'esempio del Filesystem Handler), ricorda:

- I file salvati nel container sono **temporanei**
- Usa **volume mounting** per persistere i dati:

```bash
docker run --rm \\\\
  --env-file tc-ai.env \\\\
  -v /host/path/uploads:/app/uploads \\\\  # Monta directory per i file
  -p 8000:8000 \\\\
  tc-ai:latest
```

### Best practices

1. **Multi-stage build** per immagini più leggere
2. **Volume mounting** per dati persistenti
3. **Health checks** per monitorare l'applicazione
4. **Non-root user** per sicurezza
5. **Environment variables** per configurazione

#dpKnowledge 