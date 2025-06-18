[[Vectors & vector spaces]]
#AI 
link: https://qdrant.tech/documentation/concepts/collections/
Una collection in Qdrant è un insieme denominato di punti (vettori con payload) utilizzati per le ricerche. I vettori all'interno della stessa collezione devono avere la stessa dimensionalità e vengono confrontati utilizzando una singola metrica di distanza.

I **named vectors** (vettori denominati) consentono di avere più vettori in un singolo punto, ognuno con la propria dimensionalità e requisiti di metrica.

Le **metriche di distanza** vengono utilizzate per misurare la similarità tra vettori. La scelta della metrica dipende dal metodo di ottenimento dei vettori e, in particolare, dal metodo di addestramento dell'encoder di rete neurale.

Oltre alle metriche e alla dimensione dei vettori, ogni collezione utilizza il proprio set di parametri che controlla l'ottimizzazione della collezione, la costruzione dell'indice e le operazioni di vacuum (pulizia).

## Setting up multitenancy

### Quante collezioni dovresti creare?

Nella maggior parte dei casi, dovresti utilizzare una singola collezione con partizionamento basato su payload. Questo approccio viene chiamato **multitenancy**.

### Quando creare più collezioni?

Quando hai un numero limitato di utenti e hai bisogno di isolamento. Questo approccio è flessibile, ma potrebbe essere più costoso, poiché la creazione di numerose collezioni può comportare un overhead di risorse. Inoltre, devi assicurarti che non si influenzino a vicenda in alcun modo, incluse le prestazioni.

## Creazione di una collezione

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config=models.VectorParams(size=100, distance=models.Distance.COSINE),
)
```

I vettori risiedono tutti in RAM per un accesso molto rapido. Il parametro `on_disk` può essere impostato nella configurazione del vettore. Se impostato su true, tutti i vettori risiederanno su disco. Questo abiliterà l'uso di memmaps, adatto per l'inserimento di grandi quantità di dati.

## Vettori multipli per record

È possibile avere più vettori per record. Questa funzionalità consente di avere più archivi di vettori per collezione. Per distinguere i vettori in un record, dovrebbero avere un nome univoco definito durante la creazione della collezione. Ogni vettore denominato in questa modalità ha la propria distanza e dimensione:

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config={
        "image": models.VectorParams(size=4, distance=models.Distance.DOT),
        "text": models.VectorParams(size=8, distance=models.Distance.COSINE),
    },
)
```

## Collezioni con vettori sparsi

I **vettori sparsi** sono utili per la ricerca di testo, dove ogni parola è rappresentata come una dimensione separata. Le collezioni possono contenere vettori sparsi come vettori denominati aggiuntivi insieme ai normali vettori densi in un singolo punto. A differenza dei vettori densi, i vettori sparsi devono essere denominati. Inoltre, i vettori sparsi e i vettori densi devono avere nomi diversi all'interno di una collezione.

```python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config={},
    sparse_vectors_config={
        "text": models.SparseVectorParams(),
    },
)
```

Per funzionalità come **Check collection existence**, **Delete collection**, **Update vector parameters**, **Update collection parameters** consultare la documentazione sul sito ufficiale di Qdrant.