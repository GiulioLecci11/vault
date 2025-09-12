[Settings should be parsed from the environment](https://12factor.net/config).

Per mantenere le impostazioni ordinate nell'environment puoi usare i file `.env`.

Un file `.env` non è altro che una lista di variabili d’ambiente:

```python
# .env

PORT=8001
DEBUG=True
DATABASE_URL=postgres...
SECRET_KEY=super secret
AUTH_REDIRECT_URI=https://localhost:8001/auth/callback
...
```

Per usare queste impostazioni nella tua app puoi usare [pydantic-settings](https://docs.pydantic.dev/latest/concepts/pydantic_settings/). In questo modo avrai:

- Parsing e validazione automatica
- Documentazione chiara su quali variabili servono alla tua app per funzionare
- Niente più chiamate `os.getenv` sparse ovunque

**Procedura:**

1. Crea il file `.env`
2. Crea il file `config.py` in cui definisci da dove prendere le informazioni delle variabili d’ambiente.

```python
# config.py
from pydantic_settings import BaseSettings, SettingsConfigDict
import os

current_dir = os.path.dirname(os.path.abspath(__file__))
env_dir = os.path.join(current_dir, "..", ".env")

class Settings(BaseSettings):
    app_name : str

    model_config = SettingsConfigDict(env_file = env_dir)

settings = Settings()

# main.py
from src.config import settings
```

#dpKnowledge 