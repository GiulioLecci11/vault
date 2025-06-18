[[RAG Concepts]]
[[Principal Component & Linear Discrimination Analysis]]
fonte: https://qdrant.tech/documentation/concepts/vectors/
#AI 

I *vettori* (o *embedding) sono il concetto centrale del motore di ricerca vettoriale **Qdrant*. Essi definiscono la somiglianza tra oggetti nello spazio vettoriale.  

Se una coppia di vettori è simile nello spazio vettoriale, significa che gli oggetti che rappresentano sono simili in qualche modo.  

Per ottenere una rappresentazione vettoriale di un oggetto, bisogna applicare un *algoritmo di vettorizzazione* all'oggetto. Di solito, questo algoritmo è una *rete neurale* che converte l'oggetto in un vettore di lunghezza fissa.  

---

## Tipi di Vettori

### 1. *Dense Vectors (catturano significato)*
Questo è il tipo di vettore più comune. Si tratta di una semplice lista di numeri con lunghezza fissa, in cui ogni elemento è un numero in virgola mobile (*floating-point*).  

#### *Esempio di Dense Vector*
⁠ json
[
    -0.013052909,
    0.020387933,
    -0.007869,
    -0.11111383,
    -0.030188112,
    -0.0053388323,
    0.0010654867,
    0.072027855,
    -0.04167721,
    0.014839341,
    -0.032948174,
    -0.062975034
]
La maggior parte delle **reti neurali** crea **dense vectors**.  

---

### 2. **Sparse Vectors (catturano keywords)**
I **sparse vectors** sono una variante particolare dei **dense vectors**. Matematicamente sono equivalenti, ma contengono molti **zeri**, quindi vengono memorizzati in un formato speciale per ottimizzare lo spazio.  

In **Qdrant**, i **sparse vectors** non hanno lunghezza fissa, ma viene allocata dinamicamente durante l'inserimento del vettore.  

Per definire un **sparse vector**, bisogna fornire un elenco di elementi **non nulli** e i relativi **indici**.  

#### **Esempio di Sparse Vector**
 ⁠json
{
    "indexes": [1, 3, 5, 7],
    "values": [0.1, 0.2, 0.3, 0.4]
}
### Creazione di una Collection con Sparse Vectors in Qdrant
'''python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config={},
    sparse_vectors_config={
        "text": models.SparseVectorParams(),
    },
)
### Inserimento di un Punto con Sparse Vector
'''python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            payload={},
            vector={
                "text": models.SparseVector(
                    indices=[1, 3, 5, 7],
                    values=[0.1, 0.2, 0.3, 0.4]
                )
            },
        )
    ],
)
### Creazione di una Collection con Sparse Vectors in Qdrant
'''python 
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config={},
    sparse_vectors_config={
        "text": models.SparseVectorParams(),
    },
)
### Inserimento di un Punto con Sparse Vector
'''python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            payload={},
            vector={
                "text": models.SparseVector(
                    indices=[1, 3, 5, 7],
                    values=[0.1, 0.2, 0.3, 0.4]
                )
            },
        )
    ],
)
### Ricerca con Sparse Vectors
'''python 
result = client.query_points(
    collection_name="{collection_name}",
    query=models.SparseVector(indices=[1, 3, 5, 7], values=[0.1, 0.2, 0.3, 0.4]),
    using="text",
).points
### 3. *Multivectors*
I *multivectors* permettono di memorizzare più vettori densi con la stessa forma in un unico punto.  

Ad esempio, invece di un singolo vettore denso, si può caricare una *matrice* di vettori.  

#### *Esempio di Multivector*
⁠ json
"vector": [
    [-0.013,  0.020, -0.007, -0.111],
    [-0.030, -0.055,  0.001,  0.072],
    [-0.041,  0.014, -0.032, -0.062]
]
## **Due Casi d'Uso Principali per i Multivectors**
1. **Rappresentazioni multiple dello stesso oggetto**  
   - Ad esempio, si possono archiviare **diverse angolazioni** di un'immagine dello stesso oggetto.
   - Questo approccio assume che il **payload** sia lo stesso per tutti i vettori associati.

2. **Late Interaction Embeddings**  
   - Alcuni modelli di embedding testuali generano **più vettori per lo stesso testo**.
   - Ad esempio, i modelli della famiglia **ColBERT** producono **un piccolo vettore per ogni token** nel testo.

---

## **Creazione di una Collection con Multivectors**
Per utilizzare i **multivettori**, è necessario specificare una funzione per confrontare le matrici di vettori.

Attualmente, **Qdrant** supporta la funzione `max_sim`, che somma le **massime somiglianze** tra ogni coppia di vettori nelle matrici.

 ⁠python
from qdrant_client import QdrantClient, models

client = QdrantClient(url="http://localhost:6333")

client.create_collection(
    collection_name="{collection_name}",
    vectors_config=models.VectorParams(
        size=128,
        distance=models.Distance.COSINE,
        multivector_config=models.MultiVectorConfig(
            comparator=models.MultiVectorComparator.MAX_SIM
        ),
    ),
)
## *Named Vectors*
In *Qdrant, è possibile memorizzare più vettori di **dimensioni e tipi diversi* nello stesso punto dati. Questo è utile quando è necessario definire il proprio dato con *embedding multipli* per rappresentare *diversi tipi di caratteristiche* o *modi* (es. immagini, testo, video).

### *Creazione di Named Vectors*
Per memorizzare vettori diversi per ogni punto, bisogna creare *spazi di vettori separati* nella collection. Questi spazi vengono definiti durante la creazione della collection e gestiti in modo indipendente.

⁠ python
client.create_collection(
    collection_name="{collection_name}",
    vectors_config={
        "image": models.VectorParams(size=4, distance=models.Distance.DOT),
        "text": models.VectorParams(size=5, distance=models.Distance.COSINE),
    },
    sparse_vectors_config={"text-sparse": models.SparseVectorParams()},
)
### **Inserimento di un Punto con Named Vectors**
Per inserire un punto con vettori denominati, puoi utilizzare il seguente codice:

 ⁠python
client.upsert(
    collection_name="{collection_name}",
    points=[
        models.PointStruct(
            id=1,
            vector={
                "image": [0.9, 0.1, 0.1, 0.2],
                "text": [0.4, 0.7, 0.1, 0.8, 0.1],
                "text-sparse": {
                    "indices": [1, 3, 5, 7],
                    "values": [0.1, 0.2, 0.3, 0.4],
                },
            },
        ),
    ],
)
## *Data Types per i Vettori*
Le versioni più recenti dei modelli di embedding generano vettori con *dimensioni molto grandi. Ad esempio, con il modello **OpenAI text-embedding-3-large, la dimensionalità può arrivare fino a **3072*.

L'ammontare di memoria necessaria per archiviare tali vettori cresce *linearmente* con la dimensionalità. Pertanto, è fondamentale scegliere il *tipo di dato* giusto per i vettori in base alle necessità di memoria e precisione.

### *1. Float32 (Predefinito)*
•⁠  ⁠*32-bit floating point*  
•⁠  ⁠Utilizzato di default in *Qdrant*  
•⁠  ⁠Memoria: per un embedding *OpenAI* di 1536 dimensioni, richiede *6KB* di memoria.

### *2. Float16 (Half Precision)*
•⁠  ⁠*16-bit floating point* (metà della memoria di Float32)  
•⁠  ⁠Mantiene quasi la stessa qualità di ricerca con una significativa riduzione della memoria  
•⁠  ⁠Specificare il tipo di dato nella configurazione della collection:

⁠ python
client.create_collection(
    collection_name="{collection_name}",
    vectors_config=models.VectorParams(
        size=128,
        distance=models.Distance.COSINE,
        datatype=models.Datatype.FLOAT16
    ),
)
### **3. Uint8 (Quantized)**
- **8-bit interi** (valori da 0 a 255)  
- Non è un numero in virgola mobile, ma un intero che consente un'ulteriore ottimizzazione della memoria  
- Richiede **quantizzazione** per convertire i vettori da float a uint8  
- Utilizzato da modelli di embedding come quelli di **Cohere int8 embeddings**  

Quando si utilizza **Uint8**, è necessario essere consapevoli che non tutti i modelli di embedding generano vettori nel range da 0 a 255. Pertanto, è essenziale applicare un processo di **quantizzazione** per adattare i valori float al range di **Uint8**. Alcuni provider di embedding forniscono già vettori pre-quantizzati, mentre in altri casi è necessario applicare il processo di quantizzazione manualmente.

#### **Esempio di Creazione della Collection con Uint8**
 ⁠python
client.create_collection(
    collection_name="{collection_name}",
    vectors_config=models.VectorParams(
        size=128, distance=models.Distance.COSINE, datatype=models.Datatype.UINT8
    ),
    sparse_vectors_config={
        "text": models.SparseVectorParams(
            index=models.SparseIndexParams(datatype=models.Datatype.UINT8)
        ),
    },
)
## *Quantizzazione*
Oltre alla selezione del tipo di dato, *Qdrant* può generare *rappresentazioni quantizzate* dei vettori per ottimizzare l'uso della memoria e migliorare la velocità di ricerca. La quantizzazione riduce la dimensione dei vettori, permettendo di conservare più dati nella memoria con una perdita minima di precisione.

Questo processo di quantizzazione viene applicato *in background* durante l'ottimizzazione, per selezionare rapidamente i candidati per il ri-scoraggio o anche per eseguire ricerche direttamente utilizzando le versioni quantizzate dei vettori.

L'uso della quantizzazione è utile in scenari in cui la memoria è limitata, ma la velocità di ricerca e l'efficienza sono essenziali. 

---

## *Vector Storage*
A seconda delle esigenze dell'applicazione, *Qdrant* offre diverse opzioni di archiviazione per i vettori. Tuttavia, è importante tenere presente che ci sarà sempre un compromesso tra la *velocità di ricerca* e la *memoria RAM* utilizzata. Se la memoria è limitata, l'uso di *vettori quantizzati* o *vettori a precisione inferiore* (come *Float16* o *Uint8*) può essere molto vantaggioso per mantenere un buon livello di performance.

---

### *Conclusione*
*Qdrant* è un motore di ricerca vettoriale avanzato che supporta una vasta gamma di configurazioni per la gestione dei vettori, inclusi *vettori densi, **sparsi, **multivettori* e *vettori denominati. Inoltre, offre supporto per l'ottimizzazione della memoria tramite **quantizzazione* e per la gestione di diverse *precisioni di dati*.

La scelta del giusto tipo di vettore e della precisione dei dati è fondamentale per ottimizzare le *performance* e *l'efficienza* del sistema in base alle esigenze specifiche dell'applicazione.

NOTE:
Ma non posso "migliorare" l'utilizzo del mio spazio vettoriale se so che andrò a parlare solo di un argomento specifico nella mia knowledge base? (Quindi mi ritroverei tutti i punti nella stessa porzione di vector space, come fossero ***grumi***)
Esistono 3 modi:
	-Applicare una trasformazione lineare allo spazio vet (pca o simili)
	 -Fare fine tuning del modello embedding (però dovresti hostare tu il modello ed è dura) ----Impostare un thresholding ad hoc (provare trial and error)

