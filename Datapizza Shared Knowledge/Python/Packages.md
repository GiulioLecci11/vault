## Perché serve `__init__.py` in una directory Python?

In Python, una directory che contiene un file `__init__.py` viene considerata un **package**.

### Scopo di `__init__.py`

- Serve a **segnalare a Python** che la directory va trattata come un modulo importabile.
- Permette di **inizializzare il package**, ad esempio importando sottocomponenti o definendo variabili globali.
- Senza questo file, in alcune versioni di Python (< 3.3), **non è possibile importare moduli dalla directory**.
- Anche con Python ≥ 3.3 (dove esistono i _namespace packages_), è **buona pratica** includere `__init__.py` per compatibilità e chiarezza.

### Esempio

Struttura:

```
.
├── utili/
│   ├── __init__.py
│   └── funzioni.py
```

Uso:

```python
from utili import funzioni
```

#dpKnowledge 