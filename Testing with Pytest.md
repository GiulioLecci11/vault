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