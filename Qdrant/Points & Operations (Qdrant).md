[[Vectors & vector spaces]] [[Collections (Qdrant)]]
link: https://qdrant.tech/documentation/concepts/points/
#AI 

I punti sono l'entità centrale con cui Qdrant opera. Un punto è un record composto da un vettore e un payload opzionale. Esempio:

```json
{
    "id": 129,
    "vector": [0.1, 0.2, 0.3, 0.4],
    "payload": {"color": "red"},
}
```

È possibile effettuare ricerche tra i punti raggruppati in una collezione basandosi sulla similarità vettoriale. Qualsiasi operazione di modifica dei punti è asincrona e si svolge in 2 fasi. Nella prima fase, l'operazione viene scritta nel Write-ahead-log. Dopo questo momento, il servizio non perderà i dati, anche in caso di interruzione dell'alimentazione.

## Point IDs

Qdrant supporta l'utilizzo di `interi non firmati a 64 bit` e `UUID` come identificatori per i punti. Esempi di rappresentazioni di stringhe UUID:

- semplice: `936DA01F9ABD4d9d80C702AF85C822A8`
- con trattini: `550e8400-e29b-41d4-a716-446655440000`
- urn: `urn:uuid:F9168C5E-CEB2-4faa-B6BF-329BF39FA1E4`

Ciò significa che in ogni richiesta è possibile utilizzare una stringa UUID invece di un ID numerico. Esempio:

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id="5c56c793-69f3-4fbf-87e6-c4bf54c28c26",
            payload={
                "color": "red",
            },
            vector=[0.9, 0.1, 0.1],
        ),
    ],
)
```

In HTTP:

```http
PUT /collections/{collection_name}/points
{
    "points": [
        {
            "id": 1,
            "payload": {"color": "red"},
            "vector": [0.9, 0.1, 0.1]
        }
    ]
}
```

## Vectors (Vettori)

Ogni punto in Qdrant può avere uno o più vettori. I vettori sono il componente centrale dell'architettura di Qdrant, che si basa su diversi tipi di vettori per fornire vari tipi di esplorazione e ricerca dei dati.

Tipi di vettori supportati:

- **Dense Vectors**: Vettori regolari, generati dalla maggior parte dei modelli di embedding.
- **Sparse Vectors**: Vettori senza lunghezza fissa, ma con pochi elementi non zero. Utili per la corrispondenza esatta dei token e i consigli di filtraggio collaborativo.
- **MultiVectors**: Matrici di numeri con lunghezza fissa ma altezza variabile. Solitamente ottenuti da modelli di interazione tardiva come ColBERT.

È possibile collegare più di un tipo di vettore a un singolo punto. In Qdrant, questi sono chiamati Named Vectors (Vettori denominati).

## Upload dei punti

Per ottimizzare le prestazioni, Qdrant supporta il caricamento batch dei punti. Ciò significa che puoi caricare più punti nel servizio con una sola chiamata API. Il batching permette di minimizzare l'overhead della creazione di connessioni di rete.

L'API di Qdrant supporta due modi di creare batch: orientato ai record e orientato alle colonne. Internamente, queste opzioni non differiscono e sono realizzate solo per comodità di interazione.

Esempio di batch:

```python
client.upsert(
    collection_name="{collection_name}",
    points=models.Batch(
        ids=[1, 2, 3],
        payloads=[
            {"color": "red"},
            {"color": "green"},
            {"color": "blue"},
        ],
        vectors=[
            [0.9, 0.1, 0.1],
            [0.1, 0.9, 0.1],
            [0.1, 0.1, 0.9],
        ],
    ),
)
```

Equivalente orientato ai record:

```python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            payload={
                "color": "red",
            },
            vector=[0.9, 0.1, 0.1],
        ),
        models.PointStruct(
            id=2,
            payload={
                "color": "green",
            },
            vector=[0.1, 0.9, 0.1],
        ),
        models.PointStruct(
            id=3,
            payload={
                "color": "blue",
            },
            vector=[0.1, 0.1, 0.9],
        ),
    ],
)
```

Tutte le API in Qdrant, incluso il caricamento dei punti, sono idempotenti. Ciò significa che eseguire lo stesso metodo più volte di seguito equivale a una singola esecuzione.

Se la collezione è stata creata con vettori multipli, i dati di ciascun vettore possono essere forniti utilizzando il nome del vettore:

```python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            vector={
                "image": [0.9, 0.1, 0.1, 0.2],
                "text": [0.4, 0.7, 0.1, 0.8, 0.1, 0.1, 0.9, 0.2],
            },
        ),
        models.PointStruct(
            id=2,
            vector={
                "image": [0.2, 0.1, 0.3, 0.9],
                "text": [0.5, 0.2, 0.7, 0.4, 0.7, 0.2, 0.3, 0.9],
            },
        ),
    ],
)
```

I vettori denominati sono opzionali. Durante il caricamento dei punti, alcuni vettori possono essere omessi. Ad esempio, è possibile caricare un punto solo con il vettore `image` e un secondo solo con il vettore `text`.

Quando si carica un punto con un ID esistente, il punto esistente viene prima eliminato, quindi viene inserito con solo i vettori specificati. In altre parole, l'intero punto viene sostituito e qualsiasi vettore non specificato viene impostato su null.

## Vettori Sparsi

I punti possono contenere vettori densi e sparsi. Un vettore sparso è un array in cui la maggior parte degli elementi ha un valore di zero.

È possibile sfruttare questa proprietà per avere una rappresentazione ottimizzata, per questo motivo hanno una forma diversa rispetto ai vettori densi. Sono rappresentati come un elenco di coppie `(indice, valore)`, dove `indice` è un intero e `valore` è un numero in virgola mobile. L'`indice` è la posizione (A PARTIRE DA 0) del valore non zero nel vettore. Il `valore` è il valore dell'elemento non zero.

Ad esempio, il seguente vettore:

```
[0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 2.0, 0.0, 0.0]
```

può essere rappresentato come un vettore sparso:

```
[(6, 1.0), (7, 2.0)]
```

Qdrant utilizza la seguente rappresentazione JSON nelle sue API:

```json
{
  "indices": [6, 7],
  "values": [1.0, 2.0]
}
```

Gli array `indices` e `values` devono avere la stessa lunghezza. E gli `indices` devono essere unici. Se gli `indices` non sono ordinati, Qdrant li ordinerà internamente, quindi non ci si può basare sull'ordine degli elementi.

I vettori sparsi devono essere denominati e possono essere caricati allo stesso modo dei vettori densi:

```python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            vector={
                "text": models.SparseVector(
                    indices=[6, 7],
                    values=[1.0, 2.0],
                )
            },
        ),
        models.PointStruct(
            id=2,
            vector={
                "text": models.SparseVector(
                    indices=[1, 2, 3, 4, 5],
                    values=[0.1, 0.2, 0.3, 0.4, 0.5],
                )
            },
        ),
    ],
)
```

## Modifica dei punti

Per modificare un punto, è possibile cambiare i suoi vettori o il suo payload. Ci sono diversi modi per farlo.

### Aggiornare i vettori

Questo metodo aggiorna i vettori specificati sui punti dati. I vettori non specificati rimangono invariati. Tutti i punti dati devono esistere:

```python
client.update_vectors(
    collection_name="{collection_name}",
    points=[
        models.PointVectors(
            id=1,
            vector={
                "image": [0.1, 0.2, 0.3, 0.4],
            },
        ),
        models.PointVectors(
            id=2,
            vector={
                "text": [0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2],
            },
        ),
    ],
)
```

### Eliminare vettori

Questo metodo elimina solo i vettori specificati dai punti dati. Altri vettori rimangono invariati. I punti non vengono mai eliminati:

```python
client.delete_vectors(
    collection_name="{collection_name}",
    points=[0, 3, 100],
    vectors=["text", "image"],
)
```

### Eliminare punti

```python
client.delete(
    collection_name="{collection_name}",
    points_selector=models.PointIdsList(
        points=[0, 3, 100],
    ),
)
```

### Recuperare punti per ID

```python
client.retrieve(
    collection_name="{collection_name}",
    ids=[0, 3, 100],
)
```

Questo metodo ha parametri aggiuntivi `with_vectors` e `with_payload`. Usando questi parametri, puoi selezionare le parti del punto che desideri come risultato. Escludere parti aiuta a non sprecare traffico trasmettendo dati inutili.

### Scorrere i punti

```python
client.scroll(
    collection_name="{collection_name}",
    scroll_filter=models.Filter(
        must=[
            models.FieldCondition(key="color", match=models.MatchValue(value="red")),
        ]
    ),
    limit=1,
    with_payload=True,
    with_vectors=False,
)
```

Quest'ultimo metodo restituisce tutti i punti con color="red":

```json
{
  "result": {
    "next_page_offset": 1,
    "points": [
      {
        "id": 0,
        "payload": {
          "color": "red"
        }
      }
    ]
  },
  "status": "ok",
  "time": 0.0001
}
```

## Conteggio dei punti

A volte può essere utile sapere quanti punti soddisfano le condizioni di filtro senza eseguire una ricerca reale. Tra gli altri, ad esempio, possiamo evidenziare i seguenti scenari:

- Valutazione della dimensione dei risultati per la ricerca a faccette
- Determinazione del numero di pagine per la paginazione
- Debug della velocità di esecuzione della query

```python
client.count(
    collection_name="{collection_name}",
    count_filter=models.Filter(
        must=[
            models.FieldCondition(key="color", match=models.MatchValue(value="red")),
        ]
    ),
    exact=True,
)
```

## Awaiting result (In attesa del risultato)

Se l'API viene chiamata con il parametro `&wait=false`, o se non è esplicitamente specificato, il client riceverà una conferma di ricezione dei dati:

```json
{
  "result": {
    "operation_id": 123,
    "status": "acknowledged"
  },
  "status": "ok",
  "time": 0.000206061
}
```

Questa risposta non significa che i dati siano già disponibili per il recupero. Questo usa una forma di coerenza eventuale. Potrebbe volerci un breve lasso di tempo prima che venga effettivamente elaborato poiché l'aggiornamento della collezione avviene in background. In effetti, è possibile che tale richiesta alla fine fallisca. Se si inseriscono molti vettori, si consiglia anche di utilizzare richieste asincrone per sfruttare il pipelining.

Se la logica dell'applicazione richiede la garanzia che il vettore sarà disponibile per la ricerca immediatamente dopo la risposta dell'API, usa il flag `?wait=true`. In questo caso, l'API restituirà il risultato solo dopo che l'operazione è terminata:

```json
{
  "result": {
    "operation_id": 0,
    "status": "completed"
  },
  "status": "ok",
  "time": 0.000206061
}
```