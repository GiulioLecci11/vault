## Il Problema

La gestione delle eccezioni può diventare rapidamente complessa. Ecco alcuni problemi tipici:

- Blocchi try/except **sparsi e annidati** aumentano la complessità cognitiva
- Codice duplicato per la gestione degli errori e delle risposte di errore
- Devi sapere quali eccezioni può sollevare una determinata funzione(aumenta il carico cognitivo)
- Porta facilmente a mischiare logica e codice di rete

## La Soluzione

→ Centralizza la gestione delle eccezioni.

FastAPI permette di definire [exception handler](https://fastapi.tiangolo.com/tutorial/handling-errors/#install-custom-exception-handlers) per tipi specifici di eccezioni. Quando un'eccezione viene sollevata nel tuo codice, gli handler si occuperanno di gestirla per te.

### Strategia generale

1. Capisco quali sono le eccezioni della classe che vado a wrappare
2. Catturo quelle eccezioni e ne genero di mie
    - Se invece non le catturo, in automatico saranno propagate alla funzione chiamante
3. Per ogni mia eccezione creo un handler
4. Ogni volta che cambia il sistema sottostante, aggiorno il mapping tra Eccezione classe esterna → Mia eccezione con relativo handler

## Implementazione

### Passo 1: Definisci eccezioni custom

```python
class ProjectBaseException(Exception):
    def __init__(self, message: str):
        super().__init__(message)
        self.error_type = self.__class__.__name__
        self.message = message

    def to_json(self, include_stacktrace: bool = False):
        return {
            "error_type": self.error_type,
            "message": self.message,
        } | ({"stacktrace": traceback.format_exc()} if include_stacktrace else {})

# Questo è l'errore che genero quando localmente trovo un'eccezione
class DatabaseException(ProjectBaseException):
    pass

class ResourceNotFoundException(ProjectBaseException):
    pass

```

### Passo 2: Registra handler globali

`@app.exception_handler(..)` assegna una funzione di gestione all'handler dell'app.

> Se durante l'esecuzione di una qualsiasi richiesta capita un errore di tipo DatabaseException, FastAPI non mostrerà il traceback grezzo ma userà la funzione database_exception_handler per inviare al client una risposta HTTP 500 con un JSON personalizzato.

```python
def register_exception_handlers(
    app: FastAPI, include_stacktrace: bool = False
) -> FastAPI:
    @app.exception_handler(DatabaseException)
    def database_exception_handler(request: Request, exc: DatabaseException):
        return JSONResponse(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            content=exc.to_json(include_stacktrace=include_stacktrace),
        )

    @app.exception_handler(ResourceNotFoundException)
    def resource_not_found_exception_handler(
        request: Request, exc: ResourceNotFoundException
    ):
        return JSONResponse(
            status_code=status.HTTP_404_NOT_FOUND,
            content=exc.to_json(include_stacktrace=include_stacktrace),
        )

    return app

```

### Passo 3: Cattura eccezioni specifiche nella logica applicativa

```python
def get_item_logic(id: int):
    try:
        item = db.get(id)
    except SomeDBError:
        # SomeDBError è l'errore della classe che importo -> lo catturo e genero una mia eccezione per cui ho un handler
        raise DatabaseException(f"Errore nel recupero dell'item {id}")
    if item is None:
        raise ResourceNotFoundException(f"item {id} non trovato")
    return item

```

### Passo 4: Lascia propagare le eccezioni nei tuoi endpoint

**Prima:**

```python
@app.get("/items/{id}")
def get_item(id: int):
    try:
        item = db.get(id)
        if item is None:
            raise HTTPException(status_code=404, detail="Item not found")
        return item
    except SomeDBError:
        raise HTTPException(status_code=500, detail="Database error")

```

**Dopo:**

```python
@app.get("/items/{id}")
def get_item(id: int):
    item = get_item_logic(id)
    return item

```

Quando una custom exception viene sollevata, FastAPI attiverà il tuo handler personalizzato e restituirà una `JSONResponse` con lo status code corretto.

**Importante:** Non dovresti catturare eccezioni non gestite: se succede qualcosa di grave vuoi che la tua app fallisca così puoi correggerla.

## Casi Avanzati

### Gestione specifica per endpoint

Se hai bisogno di eseguire codice specifico dell'endpoint puoi comunque farlo catturando e rilanciando le eccezioni (cerca di minimizzare questa pratica):

```python
@app.get("/items/{id}")
def get_item(id: int):
    try:
        item = get_item_logic(id)
    except ResourceNotFoundException as e:
        ...codice specifico dell'endpoint...
        raise e
    return item

```

### Handler globale per tutte le eccezioni

Se vuoi gestire TUTTE le eccezioni non gestite puoi specificare un handler per `Exception`: questo intercetterà e gestirà TUTTE le eccezioni non gestite (FastAPI già restituisce una risposta "500: internal server error" di default).

## Streaming Endpoints

### Il problema con lo streaming

Con lo streaming, se un'eccezione avviene _dopo_ che la risposta è iniziata, è troppo tardi per restituire un errore HTTP. Quindi **FastAPI non invoca gli exception handler durante le risposte streaming**. Il client ha già ricevuto un codice di successo e ogni eccezione solleverà un RuntimeError in FastAPI.

**Gli errori devono essere gestiti e restituiti come parte dei dati streaming. Il client deve esserne consapevole!**

Esempio del problema:

```python
@app.get("/stream")
def stream_endpoint():
    def stream_data():
        yield "first chunk"
        raise Exception("qualcosa è andato storto!")
    return StreamingResponse(stream_data())

```

Il client riceverà 200 OK e "first chunk", poi… niente o errore nel client. Ma non saprà che c'è stato un errore lato server.

### Soluzione per lo streaming

$$\text{Restituisci un messaggio di errore nell'output}$$

Definiamo prima i modelli base:

```python
class Encodable(ABC):
    # Questo metodo è necessario per usare i modelli Pydantic nelle risposte streaming
    # La streaming response chiama encode sull'oggetto ricevuto
    @abstractmethod
    def encode(self, encoding: str = "utf-8", errors: str = "strict") -> bytes:
        pass

class NetworkBaseModel(BaseModel, Encodable):
    def encode(self, encoding: str = "utf-8", errors: str = "strict") -> bytes:
        return (self.model_dump_json() + "\\\\n").encode(encoding, errors)

class StreamingResponseError(NetworkBaseModel):
    # usato per incapsulare l'errore in un JSON usando .encode()
    error_type: str
    message: str
    stacktrace: str | None

```

Ora definiamo un decorator che:

- I [Decoratori](https://www.notion.so/Decoratori-24d04d204fa28057aa01c4c6c9160d34?pvs=21) **intercetta le eccezioni all'interno del generatore**
- Se accade un errore, viene **"yieldato" un oggetto StreamingResponseError**, che finisce nello stream JSON
- Si può anche adattare il formato con response_format_adapter, nel caso l'API usi un formato custom

```python
def handle_streaming_exceptions(
    include_stacktrace: bool = False,
    response_format_adapter: Callable[
        [StreamingResponseError], Encodable
    ] = lambda e: e,
) -> Callable:
    """
    Decorator che wrappa i generatori streaming per gestire le eccezioni in modo elegante.
    """

    def decorator(generator_func: Callable) -> Callable:
        @wraps(generator_func)
        def wrapper(*args, **kwargs) -> Generator[str, None, None]:
            try:
                # Esegue il generatore originale
                for item in generator_func(*args, **kwargs):
                    yield item

            except (
                DatabaseException,
                ResourceNotFoundException,
            ) as e:
                yield response_format_adapter(
                    StreamingResponseError(
                        **e.to_json(include_stacktrace=include_stacktrace)
                    )
                )
            except Exception as e:
                yield response_format_adapter(
                    StreamingResponseError(
                        error_type=e.__class__.__name__,
                        message=str(e),
                        stacktrace=traceback.format_exc()
                        if include_stacktrace
                        else None,
                    )
                )

        return wrapper

    return decorator

```

### Utilizzo del decorator per streaming

```python
@app.get("/stream")
def stream_endpoint():
    @handle_streaming_exceptions
    def stream_data():
        for id in item_ids:
            item = db.get_item(id)
            yield {"data": item}
    return StreamingResponse(stream_data(), media_type="application/json")

```

Se db.get_item(id) lancia un'eccezione, l'errore verrà **catturato e inviato come JSON nello stream**.

Il client riceverà una risposta del tipo:

```json
{"data": {...}}
{"data": {...}}
{"error_type": "DatabaseException", "message": "Item not found", "stacktrace": "..."}
```