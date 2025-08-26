Tecnica che consente ai modelli LLM di scegliere quando invocare una funzione esterna (API) per ottenere dati o eseguire azioni, basandosi sul contesto.

## Funzionamento

### 1. **Definizione della Funzione**

```json
{
  "name": "get_weather",
  "description": "Ottiene il meteo di una città",
  "parameters": {
    "city": "string - nome della città",
    "units": "string - celsius o fahrenheit"
  }
}

```

### 2. **Richiesta dell'Utente**

```
Utente: "Che tempo fa oggi a Milano?"

```

### 3. **Decisione del Modello**

Il LLM riconosce che serve una funzione esterna e genera:

```json
{
  "function": "get_weather",
  "arguments": {"city": "Milano", "units": "celsius"}
}

```

### 4. **Esecuzione e Risposta**

```json
Risultato API: {"temp": 22, "condition": "soleggiato"}
Risposta finale: "A Milano oggi ci sono 22°C e il tempo è soleggiato."

```

## Vantaggi Pratici

- **Dati in tempo reale**: Accesso a informazioni aggiornate
- **Azioni concrete**: Non solo risposte, ma anche esecuzione
- **Integrazione**: Collega AI con sistemi esistenti
- **Precisione**: Evita allucinazioni sui dati specifici

## Flusso Completo

```
1. Utente fa domanda
2. LLM analizza se serve una funzione
3. LLM sceglie la funzione giusta
4. Sistema esegue la funzione
5. LLM usa il risultato per rispondere

```

## Differenza con Prompt Normale

**Senza Function Calling:**

```
Utente: "Che tempo fa a Roma?"
LLM: "Non posso accedere a dati meteo in tempo reale..."

```

**Con Function Calling:**

```
Utente: "Che tempo fa a Roma?"
LLM: → chiama get_weather("Roma") → "A Roma ci sono 18°C e piove."

```

#dpKnowledge 