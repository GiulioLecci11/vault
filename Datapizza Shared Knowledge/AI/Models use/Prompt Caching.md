Il prompt caching è una tecnica che salva le risposte a specifici prompt per evitare di computarle ogni volta. È utile in ambienti di produzione per ridurre costi e latenza.

## Analogia Semplice

Immagina un ristorante che riceve sempre le stesse domande dai clienti:

- **Prima volta**: "Avete piatti vegani?" → Chef controlla tutto il menu (5 minuti)
- **Seconda volta**: Stessa domanda → Cameriere risponde subito dalla memoria (5 secondi)

Il prompt caching funziona allo stesso modo: salva risposte a domande frequenti.

## Come Funziona

### Processo Base

```
1. Nuovo prompt arriva → Sistema controlla cache
2. Se trova risposta salvata → Restituisce immediatamente
3. Se non trova → Calcola risposta + salva in cache
4. Prossima volta → Risposta istantanea dalla cache

```

### Flusso Dettagliato

```
Utente: "Traduci 'Hello World' in italiano"

Prima richiesta:
- Cache: VUOTA
- LLM processa: "Hello World" → "Ciao Mondo" (200ms)
- Salva: ["Hello World IT"] → "Ciao Mondo"

Seconda richiesta identica:
- Cache: TROVA match
- Restituisce: "Ciao Mondo" (2ms)
- Risparmio: 99% tempo, 100% compute effort

```

## Tipi di Prompt Caching

### 1. Exact Match Caching

```
Input esatto: "Che tempo fa oggi a Milano?"
Cache key: hash("Che tempo fa oggi a Milano?")

Pro: Semplicissimo, velocissimo
Contro: Solo match perfetti

```

### 2. Semantic Caching

```
Input 1: "Che tempo fa oggi a Milano?"
Input 2: "Come è il meteo milanese oggi?"
Input 3: "Temperature attuali Milano?"

→ Tutti mappati alla stessa risposta cached

Pro: Cattura variazioni linguistiche
Contro: Più complesso, serve embedding

```

### 3. Parameter-based Caching

```
Template: "Traduci '{text}' in {language}"

Cache separata per:
- "Traduci 'ciao' in inglese" → "hello"
- "Traduci 'grazie' in inglese" → "thanks"
- "Traduci 'ciao' in francese" → "salut"

Pro: Strutturato e efficiente
Contro: Serve parsing dei parametri

```

## Strategie di Cache Management

### 1. TTL (Time To Live)

```
Contenuti con scadenza:
- Meteo: 1 ora
- News: 30 minuti
- FAQ aziendali: 1 settimana
- Traduzioni: Non scadono mai (o 1 mese)

```

### 2. LRU (Least Recently Used)

```
Cache limitata a 10,000 entries:
- Nuova richiesta arriva
- Cache piena → Rimuove entry meno usato
- Inserisce nuova risposta

```

### 3. Smart Invalidation

```
Eventi che puliscono cache:
- Update policy aziendale → Invalida FAQ
- Cambio prodotto → Invalida descrizioni
- Stagione cambia → Invalida consigli meteo

```

## Vantaggi Misurabili

### Riduzione Costi

```
Scenario: 1000 richieste/giorno, 50% cache hit

Senza cache:
- 1000 chiamate API × $0.002 = $2.00/giorno
- $730/anno

Con cache:
- 500 chiamate API × $0.002 = $1.00/giorno
- $365/anno
- Risparmio: 50% ($365)

```

### Riduzione Latenza

```
Senza cache: 500-2000ms per risposta
Con cache hit: 5-50ms per risposta

Miglioramento UX:
- Chat più reattive
- Applicazioni più fluide
- Utenti più soddisfatti

```

## Limitazioni e Sfide

### Stale Data

**Problema**: Informazioni obsolete in cache

```
Cache: "Il CEO è Mario Rossi"
Realtà: Mario Rossi si è dimesso 2 giorni fa

```

**Soluzione**: TTL appropriato + invalidazione eventi

### Cache Pollution

**Problema**: Prompt unici inquinano cache

```
"Traduci 'Ciao' in inglese" → Utile
"Traduci 'Ciao Pietro come stai oggi che è venerdì e piove...'" → Inutile

```

**Soluzione**: Filtri su lunghezza/frequenza

### Privacy Concerns

**Problema**: Dati sensibili in cache

```
"Il mio numero carta è 1234-5678-9012-3456"
→ Non deve mai essere cached!

```

**Soluzione**: Blacklist pattern + encryption

### Piccolo approfondimento (grazie Emma)

Quando vedi in un log o in una risposta che un LLM (tipo OpenAI, Gemini, Claude) ti segnala **“cached tokens”**, non significa che ha saltato l’inferenza, ma che ha **riutilizzato parte del calcolo già fatto**.

Ecco il dettaglio:

- I modelli transformer (come GPT, Claude, Gemini) usano una struttura chiamata **KV cache** (Key-Value cache).
- Durante l’inferenza, per ogni token generato, il modello deve “ricordare” tutte le rappresentazioni dei token precedenti. Se non ci fosse la cache, a ogni passo dovrebbe ricalcolare tutto da zero → costo enorme.
- La KV cache serve a memorizzare i risultati intermedi (le matrici delle “keys” e “values”) di tutti i token già processati, così che quando generi nuovi token, il modello non deve rifare i calcoli sui vecchi, ma solo sui nuovi.

Quindi:

- **“Cached tokens”** vuol dire che il modello non ha dovuto ricalcolare quei token perché li aveva già processati in precedenza.
- **Ma l’inferenza c’è comunque**: ogni nuovo token deve essere calcolato passando attraverso la rete neurale (solo che usa la cache per i token vecchi).

In altre parole: il caching riduce il lavoro, ma non elimina l’inferenza. Non è come recuperare una risposta precomputata da un database, è solo un _accorciatoia di calcolo_.

#dpKnowledge 