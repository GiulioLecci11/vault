## Introduzione al Testing del Software

Prima di tuffarci in `pytest`, è utile capire *perché* e *come* si testa il software. Scrivere test automatici è fondamentale per garantire che il tuo codice funzioni come previsto e, soprattutto, per assicurarti che nuove modifiche non rompano funzionalità esistenti.

Esistono diversi livelli di test:

*   **Unit Test (Test Unitari):** Sono i test più piccoli e specifici. L'obiettivo è testare una singola "unità" di codice in isolamento, come una funzione o un metodo di una classe. Se una funzione `somma(a, b)` deve restituire la somma di due numeri, un unit test verificherà che `somma(2, 3)` restituisca `5`, senza preoccuparsi di come questa funzione viene usata nel resto dell'applicazione.

*   **Integration Test (Test di Integrazione):** Questi test verificano che diverse unità di codice lavorino correttamente insieme. Ad esempio, potresti testare che una funzione che recupera dati dal database (`get_user_data`) e un'altra che formatta questi dati (`format_user_profile`) collaborino senza problemi.

*   **End-to-End Test (E2E):** Questi test simulano il percorso completo di un utente reale attraverso l'applicazione. Ad esempio, un test E2E per un sito di e-commerce potrebbe automatizzare i seguenti passaggi: aprire il sito, cercare un prodotto, aggiungerlo al carrello, procedere al checkout e completare l'acquisto. Sono i test più complessi e lenti, ma danno la massima confidenza sul funzionamento dell'intero sistema.

**Pytest** è un framework potentissimo e molto popolare in Python, particolarmente amato per la sua semplicità e flessibilità, che lo rendono ideale per scrivere test unitari e di integrazione.

---

# Pytest: Appunti e Spiegazioni

Per iniziare a usare pytest, devi prima installarlo:
```bash
pip install pytest
```
E poi importarlo nei tuoi file di test:

```python
import pytest
```

## Fixture

Una **fixture** è una delle funzionalità più potenti di Pytest. Immaginala come una funzione di "preparazione" che viene eseguita prima dei tuoi test per creare un ambiente o delle risorse di cui il test ha bisogno.

*   **Cosa fa?** Crea un'istanza "fresh" (nuova e pulita) di un oggetto ogni volta che un test la richiede. Questo garantisce che i test siano **isolati**: il risultato di un test non può influenzare quello successivo.
*   **Come funziona?** Si definisce una funzione e la si decora con `@pytest.fixture`. Il nome di questa funzione può poi essere usato come argomento in una funzione di test, e Pytest si occuperà di eseguirla e passare il suo risultato al test.

Nell'esempio seguente, `user_manager` è una fixture che crea un nuovo oggetto `UserManager` per ogni test che la usa.

```python
# Decoratore che trasforma una normale funzione in una fixture di Pytest
@pytest.fixture
def user_manager():
    """Crea un'istanza pulita di UserManager prima di ogni test."""
    # L'oggetto creato qui viene "iniettato" nei test che lo richiedono
    return UserManager()

# --- Test 1 ---
# Pytest vede che `test_add_user` ha bisogno di `user_manager`,
# quindi esegue la fixture e passa l'oggetto UserManager creato.
def test_add_user(user_manager):
    assert user_manager.add_user("john_doe", "john@example.com") == True
    assert user_manager.get_user("john_doe") == "john@example.com"

# --- Test 2 ---
# Anche questo test riceve la sua istanza di UserManager,
# completamente nuova e indipendente da quella usata in `test_add_user`.
def test_add_duplicate_user(user_manager):
    user_manager.add_user("john_doe", "john@example.com")
    
    # Verifichiamo che aggiungere un utente duplicato sollevi un errore
    with pytest.raises(ValueError):
        user_manager.add_user("john_doe", "another@example.com")
```

### Tipologie di Fixture: `return` vs `yield`

Ci sono due modi principali per fornire un oggetto da una fixture, con una differenza fondamentale su come viene gestita la "pulizia" (teardown) dopo il test.

*   **Con `return`**: Questo è il modo più semplice. La fixture crea e restituisce un oggetto. Una volta che il test è terminato, non hai più controllo sull'oggetto. Sarà il **Garbage Collector (G.C.)** di Python a decidere quando eliminare l'oggetto dalla memoria. È perfetto per oggetti semplici che non richiedono una pulizia manuale.

    ```python
    @pytest.fixture
    def db():
        # Crea e restituisce un'istanza del database
        return Database() 
    ```

*   **Con `yield`**: Questo approccio è più potente e ti dà il controllo sulla fase di "teardown". Il codice prima di `yield` è la fase di **setup** (preparazione). `yield` passa l'oggetto al test. Il codice dopo `yield` è la fase di **teardown** (pulizia), che viene eseguita **sempre** dopo che il test ha terminato (anche se fallisce). È essenziale per risorse che devono essere chiuse esplicitamente, come connessioni a database, file aperti, thread, ecc.

    ```python
    @pytest.fixture
    def db():
        # 1. SETUP: Eseguito prima del test
        print("\n[SETUP] Connessione al database...")
        db_connection = Database()
        
        # 2. YIELD: Passa la risorsa al test
        yield db_connection
        
        # 3. TEARDOWN: Eseguito dopo il test
        print("\n[TEARDOWN] Pulizia dati e chiusura connessione...")
        db_connection.data.clear()
        db_connection.close()
    ```



## Parametrised Settings (Test Parametrizzati)

Spesso vuoi testare la stessa funzione con molti input diversi. Invece di scrivere un test per ogni input, puoi usare la parametrizzazione.

Con il decoratore `@pytest.mark.parametrize(<nomi_parametri>, <lista_valori>)` puoi definire una serie di argomenti che verranno passati alla tua funzione di test. Pytest eseguirà il test una volta per ogni set di argomenti nella lista.

```python
# Il decoratore dice a Pytest: "Esegui 'test_is_prime' più volte"
# "num" prenderà il primo valore di ogni tupla, "expected" il secondo.
@pytest.mark.parametrize("num, expected", [
    (1, False),
    (2, True),
    (3, True),
    (4, False),
    (17, True),
    (18, False),
    (19, True),
    (25, False),
])
def test_is_prime(num, expected):
    # Ogni esecuzione del test userà una coppia (num, expected) diversa
    assert is_prime(num) == expected
```

## Mocking

Il **Mocking** (dall'inglese "to mock", imitare/prendere in giro) è una tecnica fondamentale nel testing. Consiste nel creare oggetti "falsi" (mock) che simulano il comportamento di oggetti reali.

**Perché si usa?** Per isolare il codice che stai testando da dipendenze esterne che non vuoi o non puoi controllare durante un test, come:
*   Chiamate a API esterne (che potrebbero essere lente, costose o non disponibili).
*   Accessi a un database.
*   Operazioni sul file system.

Usando un mock, puoi forzare la dipendenza esterna a comportarsi esattamente come vuoi (es. restituire un dato specifico, lanciare un errore) e testare come il *tuo* codice reagisce.

### Mockare Funzioni Esterne

Supponiamo di avere una funzione che chiama un'API, come quella di OpenAI.

```python
# my_module.py
import openai

def get_ai_response(prompt: str) -> str:
    # Questa chiamata è reale, lenta e costa denaro!
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}]
    )
    return response['choices'][0]['message']['content']
```

Per testarla senza chiamare davvero l'API, "mockiamo" la chiamata `openai.ChatCompletion.create`.

```python
# test_my_module.py
from unittest.mock import MagicMock, patch
from my_module import get_ai_response

def test_get_ai_response():
    # 1. Definiamo la risposta finta che vogliamo l'API ci restituisca
    fake_response = {
        "choices": [
            {"message": {"content": "Risposta mockata"}}
        ]
    }

    # 2. Usiamo `patch` per sostituire temporaneamente la vera funzione
    #    con un oggetto `MagicMock` che restituisce la nostra risposta finta.
    #    "my_module.openai.ChatCompletion.create" è il percorso completo all'oggetto da sostituire.
    with patch("my_module.openai.ChatCompletion.create", return_value=fake_response) as mock_create:
        
        # 3. Chiamiamo la nostra funzione. Al suo interno, la chiamata all'API
        #    sarà intercettata e sostituita dal nostro mock.
        result = get_ai_response("Ciao!")
        
        # 4. Verifichiamo che il risultato sia quello atteso
        assert result == "Risposta mockata"
        
        # 5. (Opzionale ma utile) Verifichiamo che la funzione mockata sia stata
        #    chiamata esattamente una volta e con i parametri corretti.
        mock_create.assert_called_once_with(
            model="gpt-4",
            messages=[{"role": "user", "content": "Ciao!"}]
        )
```

### Mockare Funzioni/Metodi Nostri (Wrapper)

La stessa tecnica si applica se vuoi testare una classe che hai scritto tu, isolandola da un suo metodo che fa una chiamata esterna.

```python
# my_module.py
import openai

class OpenAI:
    def get_response(self, prompt: str) -> str:
        # Questo metodo fa la chiamata reale
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[{"role": "user", "content": prompt}]
        )
        return response['choices'][0]['message']['content']
```

Il test userà `patch.object` per sostituire il metodo `get_response` solo per la durata del test.

```python
# test_my_module.py
from unittest.mock import patch
from my_module import OpenAI

def test_openai_get_response():
    # Usiamo `patch.object` per mockare il metodo `get_response` della classe `OpenAI`
    # e gli diciamo di restituire direttamente una stringa finta.
    with patch.object(OpenAI, "get_response", return_value="Risposta mockata") as mock_method:
        ai = OpenAI()
        result = ai.get_response("Ciao!")

        # Verifichiamo il risultato
        assert result == "Risposta mockata"
        
        # Verifichiamo che il nostro metodo mockato sia stato chiamato correttamente
        mock_method.assert_called_once_with("Ciao!")
```

### Domanda:  
Quando prima di definire una funzione andiamo a usare il decoratore patch (anche più di una volta) come fa pytest a capire cosa vogliamo mockare (cioè a quale parte del codice si riferisce) all'interno della funzione?

#### Risposta:
È una delle "magie" di `unittest.mock` che può confondere all'inizio, ma il meccanismo è molto logico.

La risposta breve è: **`@patch` non guarda *dentro* la funzione di test**. Modifica l'ambiente in cui la funzione di test viene eseguita *prima* che la funzione stessa parta, e poi "inietta" il mock creato come un argomento.

Vediamo il processo passo dopo passo usando il tuo esempio.

---

### Il Meccanismo di `@patch` Spiegato

Pensa ai decoratori `@patch` come a degli operai che preparano il "palcoscenico" prima che l'attore (la tua funzione di test) entri in scena.

```python
# Decoratore 1 (il più esterno)
@patch("common.dependencies.ai.scripts.baseline.utils.AzureParser")

# Decoratore 2 (il più interno, più vicino alla funzione)
@patch("common.dependencies.ai.scripts.baseline.utils.logger")

# La tua funzione di test (l'attore)
async def test_parse_document_success(self, mock_logger, mock_azure_parser):
    # ... corpo del test ...
```

Ecco cosa succede, in ordine:

#### 1. Il "Target": Dove si applica la modifica

La stringa che passi a `@patch` è un'istruzione precisa su **dove** trovare l'oggetto da sostituire.

*   `@patch("common.dependencies.ai.scripts.baseline.utils.logger")` significa:
    > "Vai nel modulo `common.dependencies.ai.scripts.baseline.utils`. Trova il nome `logger` in quel modulo e sostituiscilo temporaneamente con un oggetto `Mock`."

*   `@patch("common.dependencies.ai.scripts.baseline.utils.AzureParser")` significa:
    > "Fai la stessa cosa, ma per il nome `AzureParser` nello stesso modulo."

Questa sostituzione avviene *nell'ambiente Python*. Quando il codice all'interno della funzione `_parse_document` (la funzione che stai testando) proverà a usare `logger` o `AzureParser`, non troverà quelli veri, ma i `Mock` che `@patch` ha messo al loro posto.

**Analogia:** Immagina che il modulo `utils.py` abbia una rubrica telefonica. `@patch` non va a cambiare il numero di telefono a casa della persona reale (`logger`), ma prende la rubrica di `utils.py` e sostituisce temporaneamente il numero di `logger` con quello del suo `Mock`.

#### 2. L'Ordine di Applicazione e l'Iniezione degli Argomenti

Questa è la parte cruciale per rispondere alla tua domanda. I decoratori in Python vengono applicati **dal basso verso l'alto** (o dall'interno verso l'esterno).

1.  **`@patch("...logger")`** (il più interno) viene applicato per primo.
    *   Crea un `Mock` per `logger`.
    *   Aggiunge questo `Mock` come **primo** argomento (dopo `self`) alla funzione che sta decorando.

2.  **`@patch("...AzureParser")`** (il più esterno) viene applicato per secondo.
    *   Crea un `Mock` per `AzureParser`.
    *   Aggiunge questo `Mock` come **nuovo primo** argomento alla funzione *già decorata*, spostando gli altri argomenti a destra.

Il risultato è che l'ordine degli argomenti nella definizione della tua funzione `def test_...` deve corrispondere all'ordine inverso dei decoratori.

Visualizziamolo:

```python
# Eseguito per SECONDO. Il suo mock diventa il SECONDO argomento.
@patch("...AzureParser")
|
# Eseguito per PRIMO. Il suo mock diventa il PRIMO argomento.
@patch("...logger")
|
V
def test_parse_document_success(self, mock_logger, mock_azure_parser):
                                      ^             ^
                                      |             |
                                  Prodotto da   Prodotto da
                                @patch(logger)  @patch(AzureParser)
```

**Regola mnemonica:** Il decoratore più vicino a `def` corrisponde al primo argomento del test (dopo `self`).

#### 3. Dentro la Funzione di Test

A questo punto, quando il codice di `test_parse_document_success` inizia a essere eseguito:
*   L'ambiente è già stato modificato: `utils.logger` e `utils.AzureParser` puntano a dei Mock.
*   La tua funzione di test ha ricevuto i riferimenti a questi `Mock` come argomenti (`mock_logger` e `mock_azure_parser`), quindi puoi usarli per configurarli (`.return_value`, `.side_effect`) e per verificare come sono stati usati (`.assert_called_once()`).

### In Sintesi

`pytest` (o meglio, `unittest.mock`) non ha bisogno di "capire cosa mockare all'interno della funzione" perché:

1.  **Prepara l'ambiente prima:** La stringa in `@patch` gli dice quale nome, in quale modulo, sostituire a livello globale (ma solo per la durata del test).
2.  **Passa gli strumenti:** Inietta i `Mock` appena creati come argomenti nella funzione di test, così puoi controllarli.
3.  **Segue una regola precisa:** L'ordine degli argomenti iniettati è l'inverso dell'ordine dei decoratori scritti nel file, permettendoti di associare ogni `Mock` al suo nome di variabile corretto.

## Exception Handling (Gestione delle Eccezioni)

Un buon codice non solo fa le cose giuste, ma fallisce anche nel modo corretto quando riceve input non validi. Pytest rende facile testare che il tuo codice sollevi le eccezioni attese.

Per farlo, si usa il context manager `with pytest.raises(TipoDiEccezione)`. Il test avrà successo solo se il codice all'interno del blocco `with` solleva esattamente quel tipo di eccezione.

```python
# main.py
def add(a, b):
    return a + b

def divide(a, b):
    if b == 0:
        # Se il divisore è 0, solleviamo un'eccezione
        raise ValueError("Cannot divide by 0")
    return a / b
```

```python
# test_main.py
from main import add, divide
import pytest

def test_add():
    assert add(2, 3) == 5, "Messaggio opzionale in caso di fallimento"
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_divide():
    # Questo test passa perché il codice all'interno del `with`
    # solleva un ValueError.
    with pytest.raises(ValueError):
        divide(10, 0)

def test_divide_with_message_check():
    # Possiamo anche verificare che il messaggio dell'eccezione
    # contenga una stringa specifica.
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(10, 0)
```

## Marker (tag e filtri avanzati)

I _marker_ sono etichette che puoi applicare ai test per: 
• raggrupparli ed eseguirne solo un sotto-insieme (`pytest -m slow`) 
• indicare aspettative particolari (test che fallirà con `xfail`, test da saltare con `skip`, ecc.)

Grazie ai marker puoi organizzare e gestire agilmente suite di test di qualsiasi dimensione.

#### Marker built-in più usati

```
@pytest.mark.skip(reason="Feature non ancora implementata")
def test_future_feature():
    ...

@pytest.mark.xfail(reason="Bug #123 ancora aperto")
def test_known_bug():
    assert compute() == 42  # non passerà finché il bug non viene risolto

@pytest.mark.parametrize("n", range(5))
@pytest.mark.slow  # ipotetico marker custom
def test_heavy_computation(n):
    heavy_calc(n)
```

#### Creare marker personalizzati

Registra i marker nel file `pytest.ini` (o in `pyproject.toml`, sezione `[tool.pytest.ini_options]`) per evitare l’avviso _PytestUnknownMarkWarning_:

```
# pytest.ini
[pytest]
markers =
    slow: test che richiedono molto tempo
    integration: test che toccano componenti esterne (DB, API)
```

#### Filtrare i test con l’opzione `-m`

```
pytest -m slow                          # esegue solo i test marcati slow
pytest -m "not slow"                    # esclude i lenti
pytest -m "integration and not slow"    # combinazioni logiche
```

#### Marker condizionali

`skipif` e `xfail` permettono di saltare o aspettarsi il fallimento in base a condizioni runtime:

```
import sys

@pytest.mark.skipif(sys.platform.startswith("win"), reason="Solo per Unix")
def test_unix_only():
    ...
```

## "Tricks": Testare l'output JSON

Quando una funzione restituisce una struttura complessa come un dizionario (spesso da una conversione JSON), puoi testarne la correttezza in vari modi.

Supponiamo di avere una funzione `aggregate` che restituisce un dizionario.

```python
def test_aggregate():
    # Test 1: Verificare che l'output per un input vuoto abbia la struttura corretta
    # e che la chiave 'chunks' contenga una lista vuota.
    assert aggregate([]).get("chunks") == []
    
    # Test 2: Verificare che le chiavi attese siano presenti.
    # Questo è più robusto del test precedente se non ti importa del valore.
    assert "chunks" in aggregate([]).keys()
    assert "total_length" in aggregate([]).keys()

    # Test 3: Verificare l'output esatto con un input specifico.
    # Questo è il test più stringente.
    expected_output = {
        "chunks": ["12321"],
        "total_length": 6
    }
    assert aggregate(["12345", "12321", "1234567890"]) == expected_output
```

---

## Come Lanciare i Test

Una volta scritti i tuoi test, come li esegui?
Sappi che non dovrai MAI richiamare una funzione di test, la richiamerà pytest per te. Tu dovrai solo definirne la logica con un def

1.  **Convenzione sui Nomi:** Pytest cerca automaticamente i file di test che seguono un pattern. I nomi dei file devono iniziare con `test_` (es. `test_main.py`) o finire con `_test.py`. Anche le funzioni di test all'interno di questi file devono iniziare con `test_`.

2.  **Eseguire tutti i test:** Apri il terminale nella cartella principale del tuo progetto e digita semplicemente:
    ```bash
    pytest
    ```
    Pytest troverà ed eseguirà tutti i test nel progetto.

3.  **Comandi Utili:**
    *   **Modalità Verbosa (`-v`):** Mostra un output più dettagliato, elencando ogni test eseguito e il suo risultato (PASSATO o FALLITO).
        ```bash
        pytest -v
        ```
    *   **Eseguire test specifici (`-k`):** Esegue solo i test il cui nome contiene una certa stringa.
        ```bash
        # Esegue solo i test con "add" nel nome (es. test_add)
        pytest -k "add"
        ```
    *   **Eseguire i test di un singolo file:**
        ```bash
        pytest tests/test_main.py
        ```
    *   **Eseguire una singola funzione di test:**
        ```bash
        pytest tests/test_main.py::test_divide
        ```

[[Back End- Deep dive]]
#miscellaneous 

# Esempi da ABB: 

## Unit Test di ai_baseline_manager

Nota: Questo è un **Unit Test** (Test Unitario).

Il suo obiettivo è testare la classe `PolicyCheckingPipelineManager` in **completo isolamento**.

Tutte le sue dipendenze esterne (database, file system, agenti AI) non vengono usate, ma sono sostituite da oggetti fittizi (`Mock` e `patch`).

Non verifica l'integrazione tra componenti reali (Integration Test) né il flusso completo dell'applicazione (E2E Test).

```python
# Importa le librerie necessarie per i test.
import pytest
from unittest.mock import Mock, AsyncMock
import json
from common.dependencies.ai.ai_baseline_manager import PolicyCheckingPipelineManager
from unittest.mock import patch
from api.settings import AIAppSettings

# Importa le eccezioni personalizzate che il codice può sollevare.
# Questo permette ai test di verificare che queste eccezioni vengano lanciate correttamente.
from common.exceptions import InputValidationException, ProcessingException

# Importa un modello di stato (probabilmente un Enum) per verificare che lo stato della sessione venga aggiornato correttamente nel mock del database.
from common.dependencies.db.db_models import CheckSessionState

# Importa le eccezioni di FastAPI e il tipo UploadFile per testare la gestione degli errori a livello API e per creare mock di file caricati.
from fastapi import HTTPException, UploadFile

# Importa un'eccezione specifica dal gestore di file per testare la gestione degli errori di conversione.
from file_handler.file_handler import ConversionError


class TestPolicyCheckingPipelineManager:
    """Suite di test per la classe PolicyCheckingPipelineManager."""

    # Il decoratore @pytest.fixture definisce una funzione che fornisce risorse per i test.
    # Pytest eseguirà questa funzione prima di ogni test che la richiede come argomento.
    @pytest.fixture
    def manager(self):
        """Crea un'istanza di PolicyCheckingPipelineManager per i test."""
        # Restituire una nuova istanza per ogni test garantisce l'isolamento tra i test.
        return PolicyCheckingPipelineManager()

    @pytest.fixture
    def mock_user(self):
        """Crea un finto oggetto (mock) User per i test."""
        user = Mock()  # Crea un oggetto Mock generico.
        user.id = 1    # Gli si può assegnare qualsiasi attributo necessario per il test.
        return user

    @pytest.fixture
    def mock_crud(self):
        """Crea un finto oggetto (mock) per le operazioni CRUD (database)."""
        crud = Mock()
        # Pre-configura i valori di ritorno dei metodi del mock.
        # Quando il codice chiamerà crud.save_analysis_results(...), il mock restituirà True senza interagire con un vero database. Questo isola il test dalla logica del database.
        crud.save_analysis_results.return_value = True
        crud.complete_check_session.return_value = None
        crud.update_check_session_status.return_value = None
        return crud
        
    @pytest.fixture
    def mock_file_handler(self):
        """Crea un finto oggetto (mock) FileHandler per i test."""
        # Questo mock simula la classe che gestisce le operazioni sui file (lettura, upload, ecc.),evitando interazioni reali con il file system.
        return Mock()



    # ============= Test per il metodo privato _aggregate_cluster_results =============
    
# Questa sezione è dedicata a testare un singolo metodo privato: `_aggregate_cluster_results`.

# Testare metodi privati (quelli che iniziano con `_`) è una pratica comune per garantire che le unità logiche più piccole della tua classe funzionino come previsto, rendendo più facile diagnosticare problemi quando si testano i metodi pubblici che li utilizzano.

# Il decoratore @pytest.mark.parametrize è uno degli strumenti più potenti di pytest.
# Il suo scopo è eseguire la stessa funzione di test più volte con argomenti diversi.
# Questo evita di scrivere codice ripetitivo (principio DRY: Don't Repeat Yourself).

@pytest.mark.parametrize(
    # Il primo argomento è una stringa con i nomi dei parametri che il test riceverà, separati da una virgola.
    # Questi nomi devono corrispondere esattamente ai nomi degli argomenti nella definizione della funzione di test.
    "cluster_results,expected_conditions",
    # Il secondo argomento è una lista di tuple. Ogni tupla rappresenta una singola esecuzione del test.
    # Pytest scorrerà questa lista e, per ogni tupla, chiamerà la funzione di test passando
    # gli elementi della tupla come argomenti, nell'ordine specificato nella stringa sopra.
    [
        # Caso di test 1: Input con un solo risultato. Verifica il caso base.
        (
            ['{"commercial_conditions_details": {"1": ...}}'], # Questo valore verrà passato a `cluster_results`
            {"commercial_conditions_details": {"1": ...}}    # Questo valore verrà passato a `expected_conditions`
        ),
        # Caso di test 2: Input con due risultati. Verifica che la logica di unione/aggregazione funzioni.
        (
            ['{"...": {"1": ...}}', '{"...": {"2": ...}}'],
            {"...": {"1": ..., "2": ...}}
        ),
        # Caso di test 3: Input con tre risultati non ordinati.
        # Questo test è più complesso: verifica non solo l'aggregazione, ma anche che la funzione
        # ordini correttamente le chiavi numeriche ("1", "2", "3") e non alfabeticamente ("1", "2", "3").
        (
            ['{"...": {"3": ...}}', '{"...": {"1": ...}}', '{"...": {"2": ...}}'],
            {"...": {"1": ..., "2": ..., "3": ...}}
        ),
    ],
)
def test_aggregate_cluster_results_success(
    self, manager, cluster_results, expected_conditions
):
    """
    Testa il "percorso felice" (happy path) dell'aggregazione con vari input.
    I parametri di questa funzione vengono "iniettati" da pytest:
    - `manager`: Proviene dalla fixture `@pytest.fixture def manager(self)`, che viene eseguita prima di ogni test.
    - `cluster_results`, `expected_conditions`: Provengono dalla parametrizzazione di `@pytest.mark.parametrize`.
      Ad ogni esecuzione, pytest prende la tupla successiva dalla lista e assegna i valori.
    """
    # FASE "ACT" (Agire): Eseguiamo l'azione che vogliamo testare.
    # Chiamiamo il metodo `_aggregate_cluster_results` sull'istanza `manager` (fornita dalla fixture),
    # passandogli l'input del caso di test corrente (`cluster_results`).
    result = manager._aggregate_cluster_results(cluster_results)

    # FASE "ASSERT" (Verificare): Controlliamo che il risultato sia quello che ci aspettavamo.
    # `assert` è una parola chiave di Python, ma pytest la potenzia. Se questa asserzione fallisce, pytest non si limita a dire "AssertionError", ma mostrerà una "diff" dettagliata tra `result` ed `expected_conditions`, evidenziando esattamente dove i due dizionari non corrispondono.
    # Questo rende il debugging estremamente più rapido.
    assert result == expected_conditions

def test_aggregate_cluster_results_empty_list(self, manager):
    """
    Testa un "caso limite" (edge case): cosa succede se l'input è una lista vuota.
    I test sui casi limite sono cruciali per garantire la robustezza del codice,
    poiché molti bug si nascondono nella gestione di input anomali o vuoti.
    """
    # ACT: Chiamiamo il metodo con una lista vuota.
    result = manager._aggregate_cluster_results([])
    # ASSERT: Verifichiamo che il metodo gestisca questo caso in modo controllato,
    # restituendo un dizionario di errore specifico invece di crashare.
    assert result == {"error": "No cluster results were provided."}


@pytest.mark.parametrize(
    "invalid_results",
    [
        (["invalid json"]),                # Stringa completamente non valida.
        (['{"incomplete": json}']),        # Sintassi JSON errata.
        (['{"missing_closing_brace":']),   # JSON troncato.
        (["{malformed json}"]),            # Altro formato malformato.
    ],
)
def test_aggregate_cluster_results_invalid_json(self, manager, invalid_results):
    """
    Testa la gestione degli errori quando l'input contiene stringhe JSON non valide.
    Questo verifica la capacità del codice di gestire input corrotti senza bloccarsi.
    """
    
    # Il context manager `with pytest.raises(...)` è il modo corretto per testare le eccezioni. Dice a pytest: "Mi aspetto che il codice all'interno di questo blocco `with` sollevi un'eccezione di tipo `json.JSONDecodeError`".
    # - Se l'eccezione attesa viene sollevata, il test passa.
    # - Se viene sollevata un'eccezione diversa, il test fallisce.
    # - Se non viene sollevata NESSUNA eccezione, il test fallisce (perché ce ne aspettavamo una).
    with pytest.raises(json.JSONDecodeError):
        manager._aggregate_cluster_results(invalid_results)

def test_aggregate_cluster_results_sorting(self, manager):
    """
    Testa specificamente un dettaglio dell'implementazione: l'ordinamento numerico delle chiavi.
    Questo test è importante perché previene future "regressioni": un altro sviluppatore potrebbe modificare la funzione e cambiare accidentalmente la logica di ordinamento.
    Questo test fallirebbe, segnalando il cambiamento di comportamento non desiderato.
    """
    # Input volutamente non ordinato, con un numero a due cifre ("10") per svelare la differenza tra ordinamento alfabetico ("1", "10", "2") e numerico ("1", "2", "10").
    cluster_results = [
        '{"commercial_conditions_details": {"10": ...}}',
        '{"commercial_conditions_details": {"2": ...}}',
        '{"commercial_conditions_details": {"1": ...}}',
    ]
    # ACT
    result = manager._aggregate_cluster_results(cluster_results)

    # ASSERT
    # Estraiamo le chiavi dal dizionario risultante per controllarne l'ordine.
    keys = list(result["commercial_conditions_details"].keys())
    # Verifichiamo che l'ordine sia quello numerico corretto.
    assert keys == ["1", "2", "10"]


    # ============= Test per il metodo privato _aggregate_doc_results =============
    # Questa sezione è strutturalmente simile alla precedente e testa un altro metodo di aggregazione.

    @pytest.mark.parametrize(
        "doc_results,expected_files_count",
        [
            # ... casi di test ...
        ],
    )
    def test_aggregate_doc_results_success(
        self, manager, doc_results, expected_files_count
    ):
        """Testa l'aggregazione corretta dei risultati per documento."""
        result = manager._aggregate_doc_results(doc_results)
        
        # Le asserzioni verificano che la struttura del risultato sia corretta
        # e che contenga il numero atteso di elementi.
        assert "commercial_conditions_details" in result
        assert "1" in result["commercial_conditions_details"]
        assert len(result["commercial_conditions_details"]["1"]) == expected_files_count
        # ...

    @pytest.mark.parametrize(
        "empty_results",
        [
            ([]), ([None]), (""), ([None, "", None]),
        ],
    )
    def test_aggregate_doc_results_empty_or_invalid(self, manager, empty_results):
        """Testa l'aggregazione con risultati di documenti vuoti o invalidi."""
        result = manager._aggregate_doc_results(empty_results)
        assert result == {"error": "No doc results were provided."}

    def test_aggregate_doc_results_mixed_valid_invalid(self, manager):
        """Testa l'aggregazione con un mix di risultati validi e non validi."""
        # Questo test verifica che il metodo ignori i valori non validi (None, stringa vuota) e processi correttamente quelli validi.
        doc_results = [
            None,
            "",
            '{"commercial_conditions_details": {"1": {"file1.pdf": ...}}}',
            None,
        ]
        result = manager._aggregate_doc_results(doc_results)
        assert len(result["commercial_conditions_details"]) == 1
        # ...

    # ============= Test per il metodo privato _analyze_doc =============

    # @pytest.mark.asyncio è necessario per testare funzioni asincrone (`async def`).
    # Proviene dal plugin pytest-asyncio.
    @pytest.mark.asyncio
    
    # @patch è un decoratore di `unittest.mock` che sostituisce un oggetto con un mock durante l'esecuzione del test. (qui la classe ClusterAgentFactory) 
    @patch("common.dependencies.ai.ai_baseline_manager.ClusterAgentFactory")
    async def test_analyze_doc_success(self, mock_factory_class):
        """Testa il successo di analyze_doc con una ClusterAgentFactory fittizia."""
        # Il mock creato da @patch viene passato come argomento al test.
        # `mock_factory_class` è ora un Mock che rappresenta la classe `ClusterAgentFactory`.
        
        # Logica per rendere il test dinamico e robusto ai cambiamenti di configurazione.
        settings = AIAppSettings()
        max_policies_per_cluster = settings.max_policies_per_cluster
        test_conditions_ids = list(range(1, max_policies_per_cluster + 3))
        expected_cluster_count = (len(test_conditions_ids) + max_policies_per_cluster - 1) // max_policies_per_cluster
        
        # Configurazione del mock.
        mock_factory = Mock()
        # Quando il codice farà `ClusterAgentFactory()`, otterrà `mock_factory`.
        mock_factory_class.return_value = mock_factory

        # Creazione dinamica di "agenti" fittizi.
        mock_agents = []
        # ... (logica per preparare i risultati fittizi) ...
        for cluster_idx in range(expected_cluster_count):
            # ...
            # `AsyncMock` è la versione di `Mock` per le funzioni asincrone.
            # Simula la chiamata `await agent.call_cluster_agent()`.
            mock_agent = Mock()
            mock_agent.call_cluster_agent = AsyncMock(return_value=json.dumps(cluster_result))
            mock_agents.append(mock_agent)

        # `side_effect` è più potente di `return_value`. Assegnandogli una lista, il mock restituirà un elemento diverso della lista ad ogni chiamata successiva.
        mock_factory.create_cluster_agent.side_effect = mock_agents

        doc = {"content": "test_doc", "document_name": "test_file.pdf"}
        manager = PolicyCheckingPipelineManager()
        # `await` è necessario perché stiamo chiamando una funzione asincrona.
        result = await manager._analyze_doc(doc, test_conditions_ids)

        # Oltre al risultato, si verificano le interazioni con i mock.
        assert mock_factory_class.called  # Verifica che `ClusterAgentFactory()` sia stato chiamato.
        # Verifica che il metodo per creare agenti sia stato chiamato il numero corretto di volte.
        assert mock_factory.create_cluster_agent.call_count == expected_cluster_count
        assert json.loads(result) == expected_aggregated


    @pytest.mark.asyncio
    @patch("common.dependencies.ai.ai_baseline_manager.ClusterAgentFactory")
    async def test_analyze_doc_failures(self, mock_factory_class):
        """Testa i vari casi di fallimento di analyze_doc."""

        mock_factory = Mock()
        mock_factory_class.return_value = mock_factory
        doc = {"content": "test_doc", "document_name": "test_file.pdf"}
        manager = PolicyCheckingPipelineManager()

        # Test di validazione dell'input: documento vuoto.
        # Il parametro `match` verifica che il messaggio dell'eccezione contenga la stringa specificata.
        with pytest.raises(InputValidationException, match="Document cannot be empty"):
            await manager._analyze_doc({}, [1, 2, 3])

        # Test di validazione dell'input: ID delle condizioni commerciali vuoti.
        with pytest.raises(InputValidationException, match="Commercial conditions IDs cannot be empty"):
            await manager._analyze_doc(doc, [])

        # Test di validazione dell'input: ID non interi.
        with pytest.raises(InputValidationException, match="All commercial condition IDs must be integers"):
            await manager._analyze_doc(doc, [1, "2", 3])

        # Test di un'eccezione durante l'esecuzione dell'agente.
        mock_agent = Mock()
        # `side_effect` può anche essere usato per sollevare un'eccezione quando il mock viene chiamato.
        mock_agent.call_cluster_agent = AsyncMock(side_effect=Exception("'commercial_conditions_details'"))
        mock_factory.create_cluster_agent.return_value = mock_agent

        # Verifica che l'eccezione generica venga catturata e rilanciata come `ProcessingException`.
        with pytest.raises(ProcessingException, match="Failed to analyze document: 'commercial_conditions_details'"):
            await manager._analyze_doc(doc, [1, 2])

    # ============= Test per il metodo pubblico run_policy_checking_pipeline =============

    @pytest.mark.asyncio
    # Si possono usare più decoratori @patch per mockare diverse dipendenze contemporaneamente.
    @patch("common.dependencies.ai.ai_baseline_manager.FileHandler")
    @patch("common.dependencies.ai.ai_baseline_manager.AggregatorAgent")
    async def test_run_policy_checking_pipeline_success(
        self, mock_aggregator_class, mock_file_handler_class, manager, mock_user, mock_crud
    ):
        """Testa un'esecuzione completa e con successo della pipeline."""
        check_session_id = 123
        commercial_conditions_ids = [1, 2, 3]

        # Configurazione dei mock per il FileHandler e i suoi metodi.
        mock_file_handler = Mock()
        mock_file_handler_class.return_value = mock_file_handler
        mock_docs = [{"document_content": "...", "document_name": "contract_v1.pdf"}, ...]
        mock_file_handler.read_files = AsyncMock(return_value=mock_docs)
        
        # Dati fittizi che simulano l'output del metodo _analyze_doc
        mock_docs_results = [json.dumps({...}), json.dumps({...})]

        # Configurazione del mock per l'AggregatorAgent.
        mock_aggregator = Mock()
        mock_aggregator_class.return_value = mock_aggregator
        aggregator_result = {"overview": "...", "commercial_conditions_details": {...}}
        mock_aggregator.call_aggregator_agent = AsyncMock(return_value=json.dumps(aggregator_result))

        # `patch.object` è usato per mockare un metodo su un'istanza già esistente (`manager`).
        # È utile quando un metodo chiama un altro metodo della stessa classe.
        with patch.object(manager, "_analyze_doc", side_effect=mock_docs_results):
            result = await manager.run_policy_checking_pipeline(...)
            
            # Verifica delle chiamate ai mock.
            # `assert_called_once_with` controlla che il mock sia stato chiamato una sola volta e con argomenti specifici.
            mock_file_handler_class.assert_called_once_with(str(check_session_id))
            mock_file_handler.read_files.assert_called_once()
            mock_aggregator_class.assert_called_once()
            mock_crud.save_analysis_results.assert_called_with(...)
            mock_crud.complete_check_session.assert_called_with(...)
            
            # Verifica che il metodo non restituisca nulla in caso di successo.
            assert result is None

    @pytest.mark.asyncio
    @patch("common.dependencies.ai.ai_baseline_manager.FileHandler")
    @patch("common.dependencies.ai.ai_baseline_manager.AggregatorAgent")
    async def test_run_policy_checking_pipeline_save_analysis_results_exception(self, ...):
        """Testa la pipeline quando il salvataggio dei risultati sul DB fallisce."""
        # ... (setup simile al test di successo) ...

        # Configura il mock del CRUD per sollevare un'eccezione.
        mock_crud.save_analysis_results.side_effect = Exception("Database connection failed")

        with patch.object(manager, "_analyze_doc", ...):
            result = await manager.run_policy_checking_pipeline(...)
            
            # Verifica della gestione dell'errore.
            result_dict = json.loads(result)
            assert "error" in result_dict
            
            # Verifica che siano state chiamate le funzioni corrette per la gestione dell'errore.
            mock_crud.save_analysis_results.assert_called_once()
            # Controlla che lo stato della sessione sia stato aggiornato a FAILED.
            mock_crud.update_check_session_status.assert_called_once_with(
                mock_user.id, check_session_id, CheckSessionState.FAILED
            )
            # `assert_not_called` verifica che una funzione non sia stata eseguita, il che è fondamentale per testare la logica di fallback in caso di errore.
            mock_crud.complete_check_session.assert_not_called()

    # Gli altri test di fallimento per `run_policy_checking_pipeline` seguono una struttura simile:
    # 1. Preparare i mock.
    # 2. Configurare uno dei mock per simulare un fallimento (es. restituire False o sollevare un'eccezione).
    # 3. Eseguire il metodo sotto test.
    # 4. Verificare che l'output sia un messaggio di errore corretto.
    # 5. Verificare che le funzioni di gestione dell'errore (es. `update_check_session_status`) siano state chiamate
    #    e che le funzioni successive al punto di fallimento non lo siano.

    # ============= Test per il metodo privato _upload_file =============

    @pytest.fixture
    def mock_file(self):
        """Crea un mock di un file caricato (UploadFile di FastAPI)."""
        # `spec=UploadFile` fa sì che il mock si comporti come un vero oggetto UploadFile. Se si cerca di accedere a un attributo inesistente, il mock solleverà un errore.
        file = Mock(spec=UploadFile)
        file.filename = "test_document.pdf"
        return file

    @pytest.mark.asyncio
    async def test_upload_file_conversion_error(self, ...):
        """Testa come _upload_file gestisce una ConversionError dal FileHandler."""
        conversion_error = ConversionError("PDF conversion failed", None)
        # Simula il fallimento della conversione.
        mock_file_handler.upload_file = AsyncMock(side_effect=conversion_error)

        # `as exc_info` cattura l'eccezione sollevata per poterla ispezionare.
        with pytest.raises(HTTPException) as exc_info:
            await manager._upload_file(...)

        # Verifica che l'eccezione originale sia stata "tradotta" nella corretta HTTPException per l'API.
        assert exc_info.value.status_code == 422
        assert "File conversion failed" in exc_info.value.detail
        
        # Verifica che, anche in caso di errore, lo stato della sessione sia stato aggiornato a FAILED.
        mock_crud.update_check_session_status.assert_called_once_with(...)

    # Gli altri test per `_upload_file` seguono lo stesso schema, testando la mappatura
    # di diverse eccezioni (ValueError, IOError, HTTPException, Exception generica)
    # verso la corretta HTTPException.

    @pytest.mark.asyncio
    async def test_upload_file_success_case(self, ...):
        """Testa il caso di successo di _upload_file (nessuna eccezione)."""
        expected_filename = "processed_test_document.pdf"
        # Simula un caricamento riuscito.
        mock_file_handler.upload_file = AsyncMock(return_value=expected_filename)

        result = await manager._upload_file(...)
        
        # Verifica che il risultato sia quello atteso.
        assert result == expected_filename
        # Verifica che la funzione per aggiornare lo stato di errore NON sia stata chiamata.
        mock_crud.update_check_session_status.assert_not_called()
        
    @pytest.mark.asyncio
    async def test_upload_file_missing_parameters(self, ...):
        """Testa il comportamento con parametri mancanti (caso limite)."""
        # ...
        with pytest.raises(HTTPException) as exc_info:
            # Chiama il metodo con None per gli argomenti opzionali.
            await manager._upload_file(mock_file, mock_file_handler, None, None, None)
        
        # Il test verifica che il metodo gestisca l'errore del file handler
        # e non si blocchi a causa dei parametri None, dimostrando robustezza.
        assert exc_info.value.status_code == 422
```

## Unit test di utils.py

``` python
import pytest
import re
from unittest.mock import AsyncMock, patch, Mock, call
from common.dependencies.ai.scripts.baseline.utils import (
    _parse_document,
    VPNConnectionException,
    _doc_to_docx,
    _docx_to_pdf_libreoffice,
    _convert_to_pdf,
)


class TestUtils:
    """Suite di test per il file di utilità utils.py"""

    # ==================== test _parse_document ====================
    @pytest.mark.asyncio
    # [CASO STUDIO 1: Mocking di una classe]
    # Usiamo @patch per sostituire la classe `AzureParser` nel modulo `utils` con un Mock.
    # Questo ci permette di testare la logica di `_parse_document` senza dover
    # effettivamente istanziare un vero parser Azure, isolando il test da servizi esterni.
    @patch("common.dependencies.ai.scripts.baseline.utils.AzureParser")
    @patch("common.dependencies.ai.scripts.baseline.utils.logger")
    async def test_parse_document_success(self, mock_logger, mock_azure_parser):
        """Testa il parsing di successo di un documento."""

        # Creiamo un mock per l'ISTANZA di AzureParser.
        mock_azure_parser_instance = AsyncMock()
        # Configuriamo il valore di ritorno del metodo asincrono `a_parse`.
        mock_azure_parser_instance.a_parse.return_value = {
            "content": "test_content",
            "document_name": "test_document.pdf",
        }
        # Diciamo al mock della CLASSE di restituire la nostra istanza fittizia quando viene chiamato.
        # Questo simula la riga di codice: `parser = AzureParser()`
        mock_azure_parser.return_value = mock_azure_parser_instance

        result = await _parse_document("/test/path.pdf")

        assert result == {
            "content": "test_content",
            "document_name": "test_document.pdf",
        }
        mock_azure_parser_instance.a_parse.assert_called_once_with("/test/path.pdf")
        mock_logger.info.assert_called_once()
        mock_logger.error.assert_not_called()

    @pytest.mark.asyncio
    @pytest.mark.parametrize(
        "error_message",
        [
            "Access denied due to Virtual Network/Firewall rules",
            "(403) Access denied due to Virtual Network/Firewall rules", # Messaggio con caratteri speciali per regex
            "403",
            "Virtual Network",
            "Firewall rules",
        ],
    )


    @patch("common.dependencies.ai.scripts.baseline.utils.AzureParser")
    @patch("common.dependencies.ai.scripts.baseline.utils.logger")
    async def test_parse_document_exceptions(
        self, mock_logger, mock_azure_parser, error_message
    ):
        """Testa come la funzione gestisce le eccezioni di connessione (es. VPN)."""

        expected_match = (
            "Unable to connect to Azure Document Intelligence service. "
            "Please ensure you are connected to the VPN to access Azure resources. "
            f"Original error: {error_message}"
        )

        mock_azure_parser_instance = AsyncMock()
        # [CASO STUDIO 4: Usare side_effect per sollevare eccezioni]
        # Invece di restituire un valore, configuriamo il mock per sollevare un'eccezione quando il suo metodo `a_parse` viene chiamato. 
        # Questo è il modo standard per testare i percorsi di gestione degli errori del nostro codice.
        mock_azure_parser_instance.a_parse.side_effect = Exception(error_message)
        mock_azure_parser.return_value = mock_azure_parser_instance

        # [CASO STUDIO 2: Uso di re.escape con pytest.raises]
        # L'argomento `match` di pytest.raises usa espressioni regolari. Se il nostro `expected_match` contiene caratteri speciali come '(', ')', '.', ecc., il test fallirebbe.
        # `re.escape()` "disattiva" questi caratteri, assicurando che la corrispondenza avvenga sul testo letterale. È una best practice per rendere i test più robusti.
        with pytest.raises(VPNConnectionException, match=re.escape(expected_match)):
            await _parse_document("/test/path.pdf")

        mock_azure_parser_instance.a_parse.assert_called_once_with("/test/path.pdf")
        mock_logger.error.assert_not_called()
        mock_logger.info.assert_called_once()


    # ==================== test _doc_to_docx ====================
    # [CASO STUDIO 1: Mocking di moduli standard (Path e subprocess)]
    # Qui stiamo testando una funzione che interagisce con il file system (`Path`) e esegue comandi esterni (`subprocess`). Il patching ci permette di evitare queste operazioni reali, che sarebbero lente e dipendenti dall'ambiente.
    @patch("common.dependencies.ai.scripts.baseline.utils.Path")
    def test_doc_to_docx_exceptions(self, mock_path):
        """Testa le eccezioni di _doc_to_docx, come file non trovato o errori di conversione."""
        
        # Test 1: File non trovato
        mock_path_instance = Mock()
        # Simuliamo che `path.exists()` restituisca False.
        mock_path_instance.exists.return_value = False
        mock_path.return_value.resolve.return_value = mock_path_instance

        with pytest.raises(FileNotFoundError):
            _doc_to_docx("/nonexistent/file.doc")

        # Test 2: Errore durante la conversione (simulato da subprocess)
        mock_path_instance.exists.return_value = True # Ora simuliamo che il file esista.
        mock_path.return_value.resolve.return_value = mock_path_instance

        # Usiamo `with patch(...)` per mockare `subprocess.run` solo all'interno di questo blocco.
        with patch("common.dependencies.ai.scripts.baseline.utils.subprocess.run") as mock_subprocess_run:
            # Simuliamo un codice di uscita diverso da 0, che indica un errore.
            mock_subprocess_run.return_value.returncode = 1
            with pytest.raises(Exception):
                _doc_to_docx("/nonexistent/file.doc")

    # ==================== test _docx_to_pdf_libreoffice ====================

    @patch("common.dependencies.ai.scripts.baseline.utils.Path")
    def test_docx_to_pdf_libreoffice_exceptions(self, mock_path):
        """Testa le eccezioni per la conversione da docx a pdf."""

        # [CASO STUDIO 4: Usare side_effect per restituire una sequenza di valori]
        # La funzione testata chiama `Path()` due volte: una per il file di input e una per la directory di output. Assegnando una lista a `side_effect`, facciamo in modo che la prima chiamata a `Path()` restituisca `mock_docx_path_obj` e la seconda `mock_output_dir_obj`.
        mock_docx_path_obj = Mock()
        mock_docx_path_obj.resolve.return_value.exists.return_value = False # Il file non esiste
        mock_output_dir_obj = Mock()
        mock_output_dir_obj.resolve.return_value.exists.return_value = True # La directory esiste
        
        mock_path.side_effect = [mock_docx_path_obj, mock_output_dir_obj]

        with pytest.raises(FileNotFoundError):
            _docx_to_pdf_libreoffice("/nonexistent/file.docx", "/output/dir")
        
        # ... (il resto del test segue una logica simile)

    @patch("common.dependencies.ai.scripts.baseline.utils.subprocess.run")
    @patch("common.dependencies.ai.scripts.baseline.utils.Path")
    def test_docx_to_pdf_libreoffice_success_output_dir_created(self, mock_path, mock_subprocess_run):
        """Testa il caso di successo in cui la directory di output viene creata."""
        
        # Simuliamo che il file esista ma la directory di output no.
        mock_docx_path_obj = Mock()
        mock_docx_path_obj.resolve.return_value.exists.return_value = True
        mock_output_dir_obj = Mock()
        mock_output_dir_obj.resolve.return_value.exists.return_value = False
        mock_path.side_effect = [mock_docx_path_obj, mock_output_dir_obj]

        _docx_to_pdf_libreoffice("/test/file.docx", "/nonexistent/output")

        # Verifichiamo una specifica interazione con il mock: che il metodo `mkdir` sia stato chiamato per creare la directory, dato che non esisteva.
        mock_output_dir_obj.resolve.return_value.mkdir.assert_called_once_with(parents=True)
        mock_subprocess_run.assert_called_once()


    # ==================== test _convert_to_pdf ====================

    @patch("common.dependencies.ai.scripts.baseline.utils.logger")
    @patch("common.dependencies.ai.scripts.baseline.utils.os.remove")
    @patch("common.dependencies.ai.scripts.baseline.utils._docx_to_pdf_libreoffice")
    @patch("common.dependencies.ai.scripts.baseline.utils._doc_to_docx")
    @patch("common.dependencies.ai.scripts.baseline.utils.Path")
    def test_convert_to_pdf_docx_success(self, mock_path, mock_doc_to_docx, mock_docx_to_pdf, mock_os_remove, mock_logger):
        """Testa la conversione di successo sia per file .docx che .doc."""

        # [CASO STUDIO 3: Testare più casi in una singola funzione]
        # Invece di creare due test separati, si raggruppano i casi di successo
        # in un'unica funzione per chiarezza logica.

        # CASO 1: Conversione di un file .docx
        mock_doc_path_obj = Mock()
        mock_doc_path_obj.suffix = ".docx"
        mock_path.side_effect = [mock_doc_path_obj, Mock()]
        _convert_to_pdf("/test/file.docx", "/output/dir")
        # Verifiche per il caso 1
        mock_docx_to_pdf.assert_called_once()
        mock_os_remove.assert_called_once_with(str(mock_doc_path_obj))
        
        # ----------

        # CASO 2: Conversione di un file .doc
        # È FONDAMENTALE resettare i mock prima di eseguire il secondo caso,
        # altrimenti le chiamate del primo caso interferirebbero con le verifiche del secondo.
        mock_doc_to_docx.reset_mock()
        mock_docx_to_pdf.reset_mock()
        mock_os_remove.reset_mock()
        
        # Setup per il caso 2
        mock_doc_path_obj_2 = Mock()
        mock_doc_path_obj_2.suffix = ".doc"
        mock_doc_path_obj_2.with_suffix.return_value = "/test/file.docx"
        mock_path.side_effect = [mock_doc_path_obj_2, Mock()]
        
        _convert_to_pdf("/test/file.doc", "/output/dir")
        
        # [CASO STUDIO 5: Verifica dettagliata di chiamate multiple con `assert_has_calls`]
        # Questa verifica è più potente di un semplice controllo su `call_count`.
        # `assert_has_calls` controlla che un set di chiamate specifiche sia stato fatto,
        # indipendentemente dall'ordine e dalla presenza di altre chiamate.
        # L'oggetto `call` (da `unittest.mock`) è il modo standard per rappresentare una chiamata.
        expected_calls = [
            call(str(mock_doc_path_obj_2)),  # Verifica che il file .doc originale sia stato rimosso
            call("/test/file.docx"),         # Verifica che il file .docx intermedio sia stato rimosso
        ]
        mock_os_remove.assert_has_calls(expected_calls)
        mock_doc_to_docx.assert_called_once()


    @patch("common.dependencies.ai.scripts.baseline.utils.logger")
    @patch("common.dependencies.ai.scripts.baseline.utils.os.remove")
    @patch("common.dependencies.ai.scripts.baseline.utils._docx_to_pdf_libreoffice")
    @patch("common.dependencies.ai.scripts.baseline.utils.Path")
    def test_convert_to_pdf_docx_remove_error(
        self, mock_path, mock_docx_to_pdf, mock_os_remove, mock_logger
    ):
        """Testa cosa succede se la rimozione del file fallisce (es. per permessi)."""
        mock_doc_path_obj = Mock()
        mock_doc_path_obj.suffix = ".docx"
        mock_path.side_effect = [mock_doc_path_obj, Mock()]
        # Simuliamo che `os.remove` sollevi un'eccezione
        mock_os_remove.side_effect = OSError("Permission denied")

        # La funzione `_convert_to_pdf` è progettata per catturare questa eccezione e loggarla,
        # senza far crashare l'intera esecuzione. Pertanto, NON usiamo `pytest.raises`.
        _convert_to_pdf("/test/file.docx", "/output/dir")

        # Verifichiamo che, nonostante l'errore, la logica di conversione sia stata eseguita
        # e che l'errore sia stato correttamente loggato.
        mock_docx_to_pdf.assert_called_once()
        mock_os_remove.assert_called_once()
        mock_logger.error.assert_called_once_with(
            f"Error removing file {mock_doc_path_obj}: Permission denied"
        )
        
    # ==================== test create_clusters ====================
    # [CASO STUDIO NUOVO: Testare una Funzione Pura con Dipendenza da Configurazione]
    # `create_clusters` è una funzione "pura": non ha effetti collaterali (come scrivere file o chiamare API).
    # Il suo output dipende solo dai suoi input e da un valore di configurazione.
    # I test si concentrano sulla correttezza della logica algoritmica e sulla validazione degli input.
    # L'unica dipendenza, `settings`, viene mockata per controllare il valore di `max_policies_per_cluster`.

    @patch("common.dependencies.ai.scripts.baseline.utils.settings")
    def test_create_clusters_success(self, mock_settings):
        """Testa il "percorso felice" della logica di clustering con vari scenari."""
        # Impostiamo il valore della dipendenza mockata.
        mock_settings.max_policies_per_cluster = 3

        # Test caso normale: la lista viene suddivisa correttamente.
        result = create_clusters([1, 2, 3, 4, 5, 6, 7])
        expected = [[1, 2, 3], [4, 5, 6], [7]]
        assert result == expected

        # Test con duplicati: verifica che la funzione rimuova correttamente i duplicati
        # mantenendo l'ordine originale di apparizione degli elementi unici.
        result = create_clusters([1, 2, 2, 3, 1, 4])
        expected = [[1, 2, 3], [4]]
        assert result == expected

        # Test con un numero di elementi che è un multiplo esatto della dimensione del cluster.
        result = create_clusters([1, 2, 3, 4, 5, 6])
        expected = [[1, 2, 3], [4, 5, 6]]
        assert result == expected

    @patch("common.dependencies.ai.scripts.baseline.utils.settings")
    def test_create_clusters_input_list_validation_errors(self, mock_settings):
        """Testa gli errori di validazione per l'input `input_list`."""
        mock_settings.max_policies_per_cluster = 3

        # Test caso limite: input con una lista vuota.
        # Verifica che la funzione sollevi un'eccezione `ValueError` con un messaggio specifico,
        # proteggendo il resto del codice da input non validi.
        with pytest.raises(ValueError, match="input_list cannot be empty"):
            create_clusters([])

    @patch("common.dependencies.ai.scripts.baseline.utils.settings")
    def test_create_clusters_settings_validation_errors(self, mock_settings):
        """Testa gli errori di validazione per il parametro `settings.max_policies_per_cluster`."""

        # Test con `None`: verifica la gestione di una configurazione mancante o non valida.
        mock_settings.max_policies_per_cluster = None
        with pytest.raises(ValueError, match="settings.max_policies_per_cluster cannot be None"):
            create_clusters([1, 2, 3])

        # Test con tipo non corretto (stringa): verifica il controllo robusto dei tipi.
        mock_settings.max_policies_per_cluster = "not an int"
        with pytest.raises(TypeError, match="settings.max_policies_per_cluster must be an integer, got str"):
            create_clusters([1, 2, 3])

        # Test con valore non valido (zero): la dimensione del cluster deve essere positiva.
        mock_settings.max_policies_per_cluster = 0
        with pytest.raises(ValueError, match="settings.max_policies_per_cluster must be positive, got 0"):
            create_clusters([1, 2, 3])

        # Test con valore non valido (negativo).
        mock_settings.max_policies_per_cluster = -1
        with pytest.raises(ValueError, match="settings.max_policies_per_cluster must be positive, got -1"):
            create_clusters([1, 2, 3])

    def test_create_clusters_missing_settings_attribute(self):
        """Testa cosa succede se l'attributo `max_policies_per_cluster` non esiste proprio sull'oggetto settings."""
        # Questo è un test di robustezza ancora più spinto.
        with patch("common.dependencies.ai.scripts.baseline.utils.settings") as mock_settings:
            # Usiamo `delattr` per rimuovere l'attributo dal nostro mock, simulando
            # uno stato dell'oggetto `settings` in cui quel campo non è mai stato definito.
            # Questo è diverso da `mock_settings.max_policies_per_cluster = None`.
            if hasattr(mock_settings, "max_policies_per_cluster"):
                delattr(mock_settings, "max_policies_per_cluster")

            # La funzione dovrebbe gestire anche questo caso, probabilmente sollevando la stessa eccezione
            # del caso `None` a seguito di un `getattr(settings, '...', None)`.
            with pytest.raises(ValueError, match="settings.max_policies_per_cluster cannot be None"):
                create_clusters([1, 2, 3])

    @patch("common.dependencies.ai.scripts.baseline.utils.settings")
    def test_create_clusters_edge_cases(self, mock_settings):
        """Testa i casi limite e le condizioni al contorno della logica di clustering."""
        # I test sui casi limite sono fondamentali per scoprire bug nascosti nell'algoritmo.

        # Caso limite: dimensione del cluster = 1. Ogni elemento finisce in un cluster separato.
        mock_settings.max_policies_per_cluster = 1
        result = create_clusters([1, 2, 3])
        expected = [[1], [2], [3]]
        assert result == expected

        # Caso limite: dimensione del cluster più grande del numero di elementi.
        # Dovrebbe risultare un solo cluster contenente tutti gli elementi.
        mock_settings.max_policies_per_cluster = 10
        result = create_clusters([1, 2, 3])
        expected = [[1, 2, 3]]
        assert result == expected

        # Verifica di nuovo la rimozione dei duplicati e la conservazione dell'ordine
        # per assicurarsi che la logica funzioni correttamente in combinazione.
        mock_settings.max_policies_per_cluster = 5
        result = create_clusters([3, 1, 4, 1, 5, 9, 2, 6, 5, 3])
        # L'ordine di output atteso è [3, 1, 4, 5, 9], [2, 6] perché gli elementi vengono
        # presi nell'ordine della loro prima apparizione nella lista originale.
        expected = [[3, 1, 4, 5, 9], [2, 6]]
        assert result == expected
```

## Unit test di aggregator agent

Nota che non riporto l'intero codice ma solo le parti che permettono di approfondire nozioni non ancora viste in questo file

```python
# [CASO STUDIO NUOVO: Testare il costruttore (__init__) di una classe]

# Questi test non chiamano metodi sull'istanza dell'agente, ma verificano che
# il processo di creazione dell'istanza (`AggregatorAgent()`) funzioni come previsto.
# Questo è cruciale per classi che orchestrano diverse dipendenze al momento
# della loro inizializzazione.

class TestAggregatorAgent:
    """Suite di test per AggregatorAgent"""

    # ==================== test init ====================

    # Per testare `__init__`, dobbiamo mockare tutte le dipendenze che vengono
    # chiamate o istanziate al suo interno.
    @patch("...settings")
    @patch("...PromptsService")
    @patch("...AzureOpenAIClient")
    # [TECNICA AVANZATA: Mockare il costruttore di una classe base]
    # Qui stiamo patchando `Agent.__init__`. Questo ci permette di isolare
    # completamente la logica di inizializzazione di `AggregatorAgent` da quella
    # della sua classe genitore (`Agent`). In questo modo, verifichiamo solo
    # che `AggregatorAgent` chiami `super().__init__(...)` con i parametri giusti, senza eseguire il codice reale del costruttore della classe base.
    @patch("...Agent.__init__")
    def test_init_creates_agent_with_correct_configuration(
        self, mock_agent_init, mock_azure_client, mock_prompts_service, mock_settings
    ):
        """Testa che l'__init__ di AggregatorAgent configuri correttamente tutte le sue dipendenze."""
        
        # FASE "ARRANGE" (Preparazione):
        # Si preparano tutti i dati e i mock necessari.
        # 1. Si configurano i valori di ritorno del mock delle impostazioni.
        mock_settings.azure_openai.azure_openai_api_key = "test_api_key"
        # ... (altre impostazioni) ...
        
        # 2. Si definisce cosa devono restituire i costruttori delle classi mockate.
        mock_client_instance = Mock()
        mock_azure_client.return_value = mock_client_instance  # `AzureOpenAIClient()` restituirà `mock_client_instance`
        
        mock_prompts_service_instance = Mock()
        mock_prompts_service.return_value = mock_prompts_service_instance # `PromptsService()` restituirà `mock_prompts_service_instance`

        mock_prompt = Mock()
        mock_prompt.prompt_body = "Test system prompt"
        mock_prompts_service_instance.get_prompt.return_value = mock_prompt # Il metodo `get_prompt` restituirà questo finto prompt.

        # 3. Diciamo al mock del costruttore della classe base di non fare nulla (restituire None).
        mock_agent_init.return_value = None

        # FASE "ACT" (Azione):
        # L'azione che stiamo testando è la creazione dell'istanza stessa.
        agent = AggregatorAgent()

        # FASE "ASSERT" (Verifica):
        # Qui non verifichiamo un valore di ritorno, ma le "interazioni" avvenute durante l'init.
        # Controlliamo che ogni dipendenza sia stata chiamata una volta e con i parametri corretti.
        
        # L'AggregatorAgent ha creato un client Azure con le impostazioni corrette?
        mock_azure_client.assert_called_once_with(
            api_key="test_api_key",
            azure_endpoint="https://test.endpoint.com",
            # ... (altri parametri) ...
        )

        # Ha recuperato il prompt giusto dal servizio di prompt?
        mock_prompts_service_instance.get_prompt.assert_called_once_with(
            "aggregator_agent"
        )

        # Ha chiamato il costruttore della sua classe base (`super().__init__`)
        # passando il nome, il prompt e il client corretti?
        mock_agent_init.assert_called_once_with(
            name="aggregator_agent",
            system_prompt="Test system prompt",
            client=mock_client_instance,
        )

        # Ha impostato correttamente i suoi attributi interni?
        assert agent.prompts_service == mock_prompts_service_instance

        # PERCHÉ È IMPORTANTE: Questo tipo di test garantisce che la "ricetta"
        # per costruire un oggetto `AggregatorAgent` sia corretta. Previene bug
        # dovuti a configurazioni errate o al passaggio di parametri sbagliati
        # alle dipendenze, che potrebbero manifestarsi solo in fasi successive
        # e sarebbero più difficili da diagnosticare.

    @patch("...settings")
    @patch("...PromptsService")
    @patch("...AzureOpenAIClient")
    @patch("...Agent.__init__")
    def test_init_handles_prompts_service_error(self, ...):
        """Testa che l'__init__ gestisca correttamente un errore dal PromptsService."""
        # ... (Arrange simile al test di successo) ...

        # SIMULAZIONE DELL'ERRORE: Configuriamo il mock per sollevare un'eccezione.
        mock_prompts_service_instance.get_prompt.side_effect = Exception("Database error")

        # ACT & ASSERT: Verifichiamo che la creazione dell'oggetto fallisca
        # sollevando l'eccezione che ci aspettiamo.
        with pytest.raises(Exception, match="Database error"):
            AggregatorAgent()

        # VERIFICA POST-ERRORE: Controlliamo che, nonostante l'errore,
        # i tentativi di interazione siano comunque avvenuti.
        mock_prompts_service_instance.get_prompt.assert_called_once_with("aggregator_agent")
        
        # PERCHÉ È IMPORTANTE: Questo test assicura che la classe sia "fail-fast",
        # ovvero che fallisca immediatamente se non può essere costruita correttamente, invece di rimanere in uno stato semi-invalido che potrebbe causare errori imprevedibili in un secondo momento.
```

## Unit test di BaseClusterAgent

Anche qui riporto solo le parti di codice utili a vedere casistiche nuove di testing
Assolutamente. Questo file di test per `BaseClusterAgent` è molto ricco e introduce diverse tecniche e concetti avanzati che vale la pena approfondire.

```python
# [CASO STUDIO 1: Refactoring dei test con un metodo helper]
# In questa classe di test, molte funzioni richiedono lo stesso setup di mock di base.
# Invece di ripetere lo stesso codice di "Arrange" in ogni test, è stato creato un metodo helper privato `_setup_mocks`.
class TestBaseClusterAgent:
    
    # ... altri test ...
    
    def _setup_mocks(
        self, mock_settings, mock_azure_client, mock_prompts_service, mock_agent_init
    ):
        """Metodo helper per configurare i mock comuni a più test."""
        mock_settings.azure_openai.azure_openai_api_key = "test_api_key"
        mock_settings.azure_openai.azure_openai_endpoint = "https://test.endpoint.com"
        mock_settings.azure_openai.azure_openai_api_version = "2023-05-15"
        mock_settings.azure_openai.model_name = "gpt-4"
        mock_settings.azure_openai.temperature = 0.7
        mock_settings.db_manager = Mock()
        mock_client_instance = Mock()
        mock_azure_client.return_value = mock_client_instance
        mock_prompts_service_instance = Mock()
        mock_prompts_service.return_value = mock_prompts_service_instance
        mock_agent_init.return_value = None
    
# [CASO STUDIO 2: Testare un metodo privato che viene chiamato dal costruttore]
# La logica per testare `_create_cluster_prompt` è complessa perché questo metodo viene chiamato durante `__init__`. Per testarlo in isolamento, bisogna prima "neutralizzarlo" durante la creazione dell'oggetto e poi chiamarlo esplicitamente.

class TestBaseClusterAgent:
    # ...
    @patch(...)
    def test_create_cluster_prompt_generates_correct_output_single_policy(self, ...):
        """Testa che _create_cluster_prompt generi l'output corretto."""
        # FASE 1: Creare un'istanza dell'agente NEUTRALIZZANDO la chiamata a _create_cluster_prompt.
        # Usiamo `patch.object` per sostituire temporaneamente `_create_cluster_prompt` con un mock che restituisce un valore fittizio. Questo ci permette di creare l'oggetto `agent` senza eseguire la vera logica di `_create_cluster_prompt` che vogliamo testare.
        with patch.object(BaseClusterAgent, "_create_cluster_prompt", return_value="Mocked"):
            agent = BaseClusterAgent(test_doc, test_cluster)

        # FASE 2: Preparare i mock per la VERA chiamata.
        # Ora che abbiamo l'istanza `agent`, configuriamo i suoi servizi mockati
        # (che sono stati impostati durante l'init) per comportarsi come vogliamo
        # durante il test di `_create_cluster_prompt`.
        agent.commercial_conditions_service = mock_commercial_conditions_service_instance
        agent.prompts_service = mock_prompts_service_instance
        # ... (configurazione dei return_value dei mock) ...
        
        # FASE 3: Chiamare il metodo privato e verificare il risultato.
        # Ora chiamiamo esplicitamente il metodo che vogliamo testare sull'istanza creata.
        result = agent._create_cluster_prompt()
        assert result == expected_output

        # Questo pattern a due fasi (creazione con neutralizzazione, poi chiamata esplicita) è fondamentale per testare metodi privati che hanno una logica complessa e che vengono eseguiti all'interno del costruttore.
        # Permette un isolamento perfetto dell'unità logica da testare.

# [CASO STUDIO 3: Testare logica di retry con side_effect complesso]
# La funzione `call_cluster_agent` ha una logica di retry. Per testarla, non basta un `side_effect` semplice. Serve un `side_effect` che cambi comportamento dopo un certo numero di chiamate.
class TestBaseClusterAgent:
    # ...
    @pytest.mark.asyncio
    @patch(...)
    async def test_call_cluster_agent_success_after_retries(self, ...):
        # ... (setup iniziale dell'agente) ...
        
        # Creiamo una funzione che fungerà da `side_effect`. Questa funzione ha uno stato interno (un contatore di chiamate) che le permette di comportarsi diversamente a ogni chiamata.
        def side_effect_a_invoke(content):
            # Le prime due volte, solleva un'eccezione per simulare un fallimento.
            if side_effect_a_invoke.call_count < 2:
                side_effect_a_invoke.call_count += 1
                raise Exception(f"Failure attempt {side_effect_a_invoke.call_count}")
            # Alla terza chiamata, restituisce il risultato corretto.
            else:
                side_effect_a_invoke.call_count += 1
                return valid_response

        side_effect_a_invoke.call_count = 0  # Inizializziamo il contatore.

        with patch.object(agent, "a_invoke", side_effect=side_effect_a_invoke) as mock_a_invoke:
            # ACT: Chiamiamo la funzione che contiene il loop di retry.
            result = await agent.call_cluster_agent()

            # ASSERT: Verifichiamo il numero di chiamate e il risultato finale.
            assert mock_a_invoke.call_count == 3  # 2 fallimenti + 1 successo
            assert "error" not in json.loads(result) # Il risultato finale deve essere di successo.

        # PERCHÉ È IMPORTANTE: Questo è il modo più potente e flessibile per testare logiche complesse come retry, circuit breaker o state machine. L'uso di una funzione con stato come `side_effect` permette di simulare qualsiasi sequenza di eventi (fallimenti, timeout, successi parziali, ecc.).


# [CASO STUDIO 4: Testare la trasformazione dei dati post-chiamata]
# Questo test non si limita a verificare che l'agente chiami una dipendenza, ma controlla che il risultato restituito dalla dipendenza venga correttamente manipolato/trasformato prima di essere restituito.
class TestBaseClusterAgent:
    # ...
    @pytest.mark.asyncio
    @patch(...)
    async def test_call_cluster_agent_handles_not_found_checking_result(self, ...):
        # ARRANGE: Prepariamo una risposta fittizia dall'agente LLM in cui il `checking_result` è "Not Found".
        not_found_response = json.dumps({
            "commercial_conditions_details": {
                "1": {
                    "filename": {
                        "extracted_information": {"information_content": "This should be ignored", ...},
                        "checking_result": "Not Found",
                        ...
                    }
                }
            }
        })
        
        with patch.object(agent, "a_invoke", return_value=not_found_response):
            # ACT
            result = await agent.call_cluster_agent()
            
            # ASSERT: Verifichiamo la trasformazione.
            # La logica di business di `call_cluster_agent` prevede che se il risultato è "Not Found", il campo `extracted_information` debba essere impostato a `None`.
            # Questo test verifica specificamente questa regola di trasformazione.
            file_data = json.loads(result)["commercial_conditions_details"]["1"]["test_contract.pdf"]
            assert file_data["extracted_information"] is None
```

## Test del DB

### Unit Test o Integration Test?

Questo file si colloca in una zona grigia, ma pende decisamente verso l'**Integration Test**.

*   **Perché è un Integration Test**: I test non mockano il database. Usano una vera istanza di PostgreSQL (`db_manager`) per verificare che i metodi della classe `CRUD` interagiscano correttamente con un database reale. Si testa l'integrazione tra la logica applicativa (`CRUD`) e il sistema di persistenza (il database PostgreSQL).
*   **Perché ha elementi di Unit Test**: Il database viene resettato prima di ogni esecuzione e ogni test è avvolto in una transazione che viene annullata (`rollback`). Questo garantisce un **isolamento** completo tra i singoli test, una caratteristica tipica degli unit test. Non ci sono dipendenze da dati preesistenti nel DB.

In sintesi, è un **integration test a livello di componente**, che testa l'integrazione tra il codice e il DB in un ambiente controllato e isolato.

```python
# [CASO STUDIO 1: Fixture per la gestione della connessione al database]
# Questa fixture è il cuore di tutti i test in questo file.
@pytest.fixture()
def db_manager():
    """Fornisce un DBManager connesso a un database di test."""
    # Legge l'URL del database da una variabile d'ambiente, con un fallback
    # a un'istanza locale. Questo rende i test flessibili per essere eseguiti
    # sia localmente che in un ambiente di CI (Continuous Integration).
    db_url = os.getenv("DATABASE_URL", "postgresql+psycopg://...")

    manager = DBManager(db_url)
    
    # SETUP: Prima di iniziare i test, si assicura che il database sia pulito e
    # che lo schema (tabelle, ecc.) sia aggiornato all'ultima versione del modello dati.
    # Questo garantisce uno stato di partenza noto e consistente per ogni test run.
    SQLModel.metadata.drop_all(manager.engine)
    SQLModel.metadata.create_all(manager.engine)

    # `yield` è la parola chiave che trasforma una funzione in un generatore.
    # In una fixture, tutto ciò che viene prima di `yield` è il SETUP.
    # Il valore "yieldato" (in questo caso `manager`) è ciò che viene passato al test.
    yield manager

    # TEARDOWN: Il codice dopo `yield` viene eseguito alla fine del test.
    # In questo caso, non c'è pulizia qui perché viene gestita da un'altra fixture.

# [CASO STUDIO 2: Fixture con `autouse=True` per la gestione delle transazioni]
# Questa è una tecnica avanzata e potentissima per garantire l'isolamento dei test del database.
@pytest.fixture(autouse=True)
def _transactional_tests(db_manager):
    """Avvolge ogni test in una transazione che viene annullata alla fine."""
    # `autouse=True` significa che questa fixture viene eseguita automaticamente
    # per OGNI test in questo file, senza bisogno di richiederla come argomento.
    
    # SETUP:
    # 1. Apre una singola connessione al DB per la durata del test.
    connection = db_manager.engine.connect()
    # 2. Avvia una transazione "esterna".
    outer_tx = connection.begin()


    # [TECNICA AVANZATA: Monkey-Patching di un'istanza]
    # Sovrascrive temporaneamente il metodo `get_session` del `db_manager`.
    # La nuova versione non apre una nuova connessione, ma crea una "transazione nidificata" (un SAVEPOINT SQL) all'interno della transazione esterna già aperta.
    # Questo fa sì che le operazioni di `session.commit()` all'interno del codice CRUD non vengano scritte permanentemente sul disco, ma solo nel savepoint.
    db_manager.get_session = _get_session_con_savepoint # Semplificazione del nome

    try:
        # Esegue il codice del test.
        yield
    finally:
        # TEARDOWN:
        # Annulla la transazione esterna. Questo cancella TUTTE le modifiche
        # fatte durante il test (inclusi i commit nei savepoint), riportando
        # il database allo stato esatto in cui si trovava prima del test.
        outer_tx.rollback()
        connection.close()

    # PERCHÉ È FONDAMENTALE: Questo pattern garantisce che ogni test parta da un
    # database vuoto e non lasci "sporcizia" per i test successivi. È il modo
    # migliore per avere test di database veloci, affidabili e indipendenti.


# [CASO STUDIO 3: Testare vincoli di integrità del database]
# Questo test non verifica solo la logica Python, ma anche i vincoli definiti
# a livello di schema del database (es. un campo `UNIQUE`).
def test_create_commercial_condition_duplicate_raises(crud):
    """Verifica che la creazione di un record con un ID duplicato sollevi un'eccezione."""
    # ARRANGE: Crea un primo record con un certo ID.
    crud.create_commercial_condition(condition_id=20, ...)

    # ACT & ASSERT: Tenta di creare un secondo record con lo STESSO ID.
    # Il codice CRUD cattura l'`IntegrityError` del database (causato dal vincolo UNIQUE)
    # e lo rilancia come un'eccezione personalizzata `IntegrityException`.
    # Il test verifica che questa eccezione venga correttamente sollevata.
    with pytest.raises(IntegrityException):
        crud.create_commercial_condition(condition_id=20, ...)


# [CASO STUDIO 4: Testare logica dipendente dal tempo modificando i dati direttamente]
# Questo test deve verificare una funzione che opera su record "vecchi".
# Invece di usare `time.sleep()`, che renderebbe il test lento, si modifica
# direttamente il timestamp del record nel database.
def test_update_running_check_sessions(crud, db_manager):
    """Verifica che le sessioni in stato RUNNING "vecchie" vengano marcate come FAILED."""
    # ARRANGE: Crea una sessione di test.
    cs = crud.create_check_session("user1", [1], ["doc"], "Session")

    # MANIPOLAZIONE DIRETTA DEL DB:
    # Si ottiene una sessione diretta al database per modificare il record appena creato.
    with db_manager.get_session() as session:
        obj = session.get(type(cs), cs.id)
        # Si imposta il timestamp di creazione a 30 minuti nel passato.
        obj.created_at = datetime.now() - timedelta(minutes=30)
        session.commit() # Questo commit scrive solo nel savepoint della transazione

    # ACT: Si chiama la funzione che deve trovare e aggiornare i record "vecchi".
    crud.update_running_check_sessions("user1")

    # ASSERT: Si verifica che lo stato del record sia stato effettivamente cambiato in FAILED.
    updated = crud.get_check_session("user1", cs.id)
    assert updated.state == CheckSessionState.FAILED

```
