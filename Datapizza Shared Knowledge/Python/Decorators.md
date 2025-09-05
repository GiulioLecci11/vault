## Cos'è un decoratore?

Un **decoratore** in Python è una funzione che **prende un'altra funzione come argomento** e **restituisce una nuova funzione** che solitamente estende o modifica il comportamento della funzione originale — senza modificarne direttamente il codice.

In pratica:

> È una scorciatoia elegante per applicare lo stesso "comportamento extra" a più funzioni.

---

**IMPORTANTE**: usa sempre `@wraps(f)` prima del `wrapper` altrimenti il `__name__` della funzione wrappata e la sua docstring verranno sovrascritte da quelli del wrapper.

### Esempio base

```python
def decoratore(f):
	@wraps(f)
    def wrapper():
        print("Prima della funzione")
        f()
        print("Dopo la funzione")
    return wrapper

@decoratore
def saluta():
    print("Ciao!")

saluta()
```

Output

```python
Prima della funzione
Ciao!
Dopo la funzione
```

## Decoratore Generico

Ha argomenti dinamici (`*args, **kwargs`)

```python
def logger(f):
	@wraps(f)
    def wrapper(*args, **kwargs):
        print(f"Chiamata a {f.__name__} con argomenti {args} {kwargs}")
        result = f(*args, **kwargs)
        print(f"Risultato: {result}")
        return result
    return wrapper

@logger
def somma(a, b):
    return a + b

somma(2, 3)

```

```python
import time
from functools import wraps

def timing(f):
	    @wraps(f)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = f(*args, **kwargs)
        end = time.time()
        print(f"{f.__name__} ha impiegato {end - start:.4f} secondi")
        return result
    return wrapper

@timing
def lavoro():
    time.sleep(1)

lavoro()

```

Output

```python
Chiamata a somma con argomenti 2 3
```

#dpKnowledge 