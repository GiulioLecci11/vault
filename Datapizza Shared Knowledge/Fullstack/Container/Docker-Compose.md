Il file `docker-compose.yml` serve a giostrare i vari container che vai a definire e li avvia in base alla sequenza che gli definisci tu.

Per lanciarlo: `docker compose up`

Consente di descrivere:

- servizi (containers)
- volumi (persistenza)
- reti (comunicazione)
- variabili d'ambiente

# Struttura tipica di un `docker-compose.yml`

```yaml
version: '3.9'  # Specifica la versione del file di configurazione

services:  # Sezione principale, che definisce i container
  web:  # Nome del servizio
    image: nginx:latest  # Immagine Docker da usare
    ports:
      - "80:80"  # Espone la porta 80 del container sulla porta 80 dell'host
    volumes:
      - ./html:/usr/share/nginx/html  # Monta un volume
    networks:
      - frontend

  app:
    build:
      context: ./app  # Costruisce da una directory
      dockerfile: Dockerfile  # Specifica un Dockerfile custom
    environment:
      - ENV=production  # Variabili d’ambiente
      - DEBUG=false
    depends_on:
      - db  # Dipende dal servizio db
    networks:
      - backend
      - frontend

  db:
    image: postgres:15
    restart: always  # Politica di restart
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - dbdata:/var/lib/postgresql/data
    networks:
      - backend

volumes:  # Definizione dei volumi
  dbdata:

networks:  # Definizione delle reti
  frontend:
  backend:
```

Un file `docker-compose.yml` è utilizzato per definire e gestire applicazioni **multi-container** in Docker. La struttura tipica segue il formato YAML e consente di descrivere:

- I servizi (containers)
- I volumi (persistenza)
- Le reti (comunicazione)
- Le variabili d'ambiente
- Altri aspetti della configurazione.

## Spiegazione dettagliata delle sezioni

### `version`

- Indica la **versione del file di configurazione** Docker Compose.
- Le versioni comuni sono `'3'`, `'3.8'`, `'3.9'`, a seconda del supporto Docker.

---

### `services`

- Sezione principale dove vengono definiti i **container** che compongono l'applicazione.

Ogni servizio può includere:

|Chiave|Descrizione|
|---|---|
|`image`|Immagine Docker da utilizzare (es. `nginx:latest`)|
|`build`|Costruisce l'immagine da una directory o Dockerfile|
|`ports`|Mappa le porte `host:container`|
|`volumes`|Monta volumi o file system (`host_path:container_path`)|
|`environment`|Definisce variabili d'ambiente|
|`env_file`|Importa variabili da un file `.env`|
|`depends_on`|Specifica l’ordine di avvio dei container (non gestisce la "prontezza")|
|`command`|Sovrascrive il comando `CMD` del Dockerfile|
|`restart`|Definisce la politica di restart (`no`, `always`, `on-failure`, `unless-stopped`)|
|`networks`|Collega il servizio a una o più reti definite|
|`volumes`|Associa volumi per persistere dati o condividere file|

---

### `volumes`

- Definisce i **volumi nominati**, usati per persistere dati tra i riavvii dei container.
- Sintassi breve (`nomevolume:`) o avanzata (`driver`, `external`, ecc.).

---

### `networks`

- Specifica le **reti Docker personalizzate** per controllare come i container comunicano tra loro.
- Si può specificare il tipo di rete (bridge, overlay, host) o opzioni aggiuntive.

---

## Esempi di uso avanzato

### Variabile d’ambiente da file

```yaml
env_file:
  - .env
```

### Restart automatico

```yaml
restart: unless-stopped
```

### Override del comando di default

```yaml
command: python app.py
```

### Collegamento a una rete esistente

```yaml
networks:
  frontend:
    external: true
```

---

## Best Practices

- Usa **volumi nominati** per la persistenza.
- Gestisci variabili sensibili tramite `.env` file.
- Definisci **reti separate** per isolare i servizi.
- Usa `depends_on` solo per sequenza, non per health checks.
- Mantieni il file semplice e leggibile; in progetti complessi, suddividi con **override files** (es. `docker-compose.override.yml`).

#dpKnowledge 