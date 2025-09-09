Tipologie

- **Unit test**: test piccoli e veloci (1 sola funzionalità). Li lanci di continuo ad ogni modifica. Può essere:
    - deterministico
    - non deterministico: tipicamente per le chiamate ai client
- **Integration test**: verificano che diverse unità di codice lavorino correttamente insieme.
- **End to End test**: simulano il percorso completo di un utente reale attraverso l'applicazione.

---

### Unit Test

- riproducibile: stesso risultato per ogni run → deterministico (tendenzialmente).
- veloce
- Comodi se poi la funzione in futuro verrà refactored, in modo da vedere se l'output non cambia.
- Con `assert` verifichi.
- **Isolamento**: Idealmente ogni unit test dovrebbe essere indipendente dagli altri e non dipendere da risorse esterne (DB, API, filesystem). Se necessario, si usano mock/stub/fake.
- **Copertura**: L'obiettivo è coprire tutti i rami logici della funzione/unità testata.

---

### Integration Test

- Testano l'integrazione tra più componenti (es: controller + DB, API + servizio esterno).
- Possono essere più lenti e meno deterministici degli unit test.
- Spesso richiedono setup e teardown di risorse (es: database temporaneo, server di test).
- Utili per scoprire problemi di configurazione, serializzazione, compatibilità tra moduli.

---

### End to End (E2E) Test

- Simulano il comportamento reale dell’utente (es: Selenium, Playwright, Cypress).
- Testano l’intero stack: frontend, backend, database, servizi esterni.
- Più lenti e costosi da mantenere, ma fondamentali per garantire che “tutto funzioni insieme”.
- Spesso si eseguono solo in CI/CD o prima di una release.

---

### Best Practice

- **Piramide dei test**: molti unit test, meno integration, pochi E2E.
- **Automazione**: tutti i test dovrebbero essere automatizzati e lanciabili con un solo comando.
- **Continuous Integration**: i test devono girare ad ogni push/merge.
- **Nomenclatura**: i file di test devono iniziare con `test_` e le funzioni con `test_` per essere riconosciuti da pytest.
- **Fixture**: usa le fixture di pytest per setup/teardown di risorse condivise.
- **Mocking**: usa `unittest.mock` o `pytest-mock` per isolare le dipendenze esterne.

---

### Esempio di struttura progetto con Pytest

```
project/
├── app/
│   └── main.py
├── tests/
│   ├── test_main.py
│   ├── test_integration.py
│   └── conftest.py

```

In Pytest i test dentro una classe puoi lanciare tutti i test in contemporanea con pytest e lui cerca i file di test contenuti nei vari percorsi, che hanno `nomefile` che inizia con `test_nomefile`.

#dpKnowledge 