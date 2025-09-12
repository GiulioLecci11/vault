**Redis** (Remote Dictionary Server) è un database in-memory, open-source, di tipo key-value, spesso utilizzato come sistema di caching, broker di messaggi o archivio dati temporaneo.

## Caratteristiche principali

- **In-memory**: Redis conserva i dati direttamente nella memoria RAM, rendendolo estremamente veloce per operazioni di lettura e scrittura.
- **Key-Value Store**: I dati sono memorizzati sotto forma di coppie chiave-valore. Le chiavi possono essere stringhe, mentre i valori possono essere di diversi tipi.
- **Tipi di dati supportati**:
    - Stringhe
    - Liste
    - Set
    - Set ordinati (Sorted Sets)
    - Hash
    - Bitmaps
    - HyperLogLogs
    - Stream

## Casi d'uso comuni

- **Caching**: Usato per memorizzare temporaneamente dati costosi da calcolare o riottenere.
- **Session Storage**: Spesso utilizzato per memorizzare sessioni utente in applicazioni web.
- **Code di messaggi**: Grazie al supporto per liste e stream, Redis può essere usato per gestire code di lavoro e sistemi di messaggistica.
- **Classifiche in tempo reale**: I Sorted Sets lo rendono adatto per classifiche aggiornate frequentemente (es. in giochi o sistemi di punteggio).
- **Pub/Sub**: Redis implementa un sistema di pubblicazione/sottoscrizione per la comunicazione tra servizi.

## Persistenza

Sebbene Redis operi in memoria, supporta la **persistenza su disco** tramite:

- **Snapshot (RDB)**: Salvataggi periodici dei dati su disco.
- **Append-only file (AOF)**: Log di tutte le operazioni eseguite, per poter ricostruire lo stato del database in caso di crash.

## Replica e Scalabilità

- Redis supporta **replica master-slave**, permettendo la distribuzione del carico di lettura.
- Con Redis Cluster è possibile ottenere **sharding automatico**, utile per scalare orizzontalmente.

## Performance

Grazie all'architettura in-memory e al fatto che è scritto in C, Redis offre prestazioni molto elevate, con latenze nell'ordine dei microsecondi.

## Quando usarlo (e quando no)

Redis è ideale per:

- Caching
- Elaborazioni in tempo reale
- Sistemi distribuiti che necessitano di sincronizzazione rapida

Non è invece adatto per:

- Dati a lungo termine che non possono essere persi
- Database relazionali o con schema complesso

## Conclusione

Redis è uno strumento potente per migliorare le performance e la scalabilità di un'applicazione, soprattutto quando si ha bisogno di accesso rapido a dati temporanei o condivisi tra più servizi.

#dpKnowledge 