Containerizzo tutto e giro dentro un server i vari container.

Possono essere:

- Windows: per girare componenti Windows
- Linux: per girare componenti linux, sono più veloci rispetto quelli Windows

### Caratteristiche

- isolamento: sono isolati
- portabilità
- leggerezza: condividono il kernel del so
- scalabilità

Un container contiene solo le librerie utili, mentre le macchine virtuali contengono anche il sistema operativo e un sacco di cose inutili.

![[Pasted image 20250910094048.png]]

Con la virtualizzazione devo usare un HyperVisor (es. VirtualBox) per creare le varie VM, con i container è tutto molto più snello, come se fossero dei virtual environments.

### Architettura

- Client: il terminale da cui andiamo a richiamare Docker
- Server: Docker Desktop che runna sulla macchina

### Esempi di comandi Docker

- Per creare/runnare da Docker l'immagine di PostgreSQL , esegui nel terminale il comando:

```bash
docker run --name postgres-test \\\\
-e POSTGRES_USER=testuser \\\\
-e POSTGRES_PASSWORD=testpass \\\\
-e POSTGRES_DB=testdb \\\\
-p 5432:5432 \\\\ # porte
-d postgres:17 # programma:versione
```

- Per entrare in un container

```bash
docker exec -it postgres-test bash
```

- Per vedere i container in esecuzione

```bash
docker ps
```

- Per stoppare un container

```bash
docker stop postgres-test
```

### Portare progetto Python in Container

1. Devo usare il **Dockerfile** in cui specifico la versione di python e di linux usata, esempio:

```bash
# DOCKERFILE

FROM python:3.9-alpine # immagine python di base
COPY app.py /app.py # copia il tuo codice python all'interno della root del container
CMD ['python','/app.py'] # quando esegue il container esegue python e richiama app.py
```

1. Devo costruire l'immagine usando Docker Build

```bash
docker build -t mio-container .
```

1. Creo il container e lo eseguo

```bash
docker run mio-container
```

#dpKnowledge 