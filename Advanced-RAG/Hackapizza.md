#AI 
https://github.com/ECaET/HackaPizza_2025

### Soluzione basata su Knowledge Graph per la Galassia Gastronomica

## Architettura della soluzione

La soluzione si basa sull'utilizzo di un Knowledge Graph per salvare le principali entità presenti all'interno della Galassia (Pianeti, Ristoranti, Piatti, Ingredienti, ...) e le loro rispettive relazioni (es. un Ristorante `--OFFRE_IL_PIATTO-->` Piatto).

Il processo si articola in due fasi principali:

1. **Costruzione del grafo**:
    - I documenti vengono processati tramite un LLM (`gpt-4o-mini`) per estrarre entità e relazioni in modo strutturato
    - L'output di questo step sono file JSON (generati con lo script `menu_to_json.py`)
    - I file JSON vengono letti e utilizzati per generare query Cypher che popolano il grafo (script `graph_construction.py`)
2. **Retrieval**:
    - Le domande in linguaggio naturale vengono convertite in query Cypher per ottenere le entità che soddisfano la domanda (script `graph_retrieval.py`)

## File di dati disponibili

### `Misc/distanze.csv`

- CSV contenente la matrice delle distanze in anni luce tra i pianeti con ristoranti
- Utile per domande del tipo: "Quali sono i piatti disponibili nei ristoranti entro 126 anni luce da Cybertron, quest'ultimo incluso, che non includono Funghi dell'Etere?"

### `Codice Galattico/Codice Galattico.pdf`

- Documento legislativo contenente:
    - Limiti quantitativi per l'utilizzo di alcuni ingredienti nella preparazione dei piatti
    - Vincoli relativi alle certificazioni necessarie agli chef per utilizzare specifiche tecniche di preparazione
    - Descrizione degli Ordini (es. Ordine della Galassia di Andromeda, Ordine dei Naturalisti)
- Utile per domande del tipo: "Che piatti posso mangiare [...] da un chef che ha le corrette licenze e certificazioni descritte dal Codice di Galattico?"

### `Misc/Manuale di cucina.pdf`

- Manuale che include:
    - Elenco e descrizione delle certificazioni che uno chef può acquisire
    - Elenco degli ordini professionali gastronomici a cui uno chef può aderire
    - Elenco e descrizione delle tecniche culinarie di preparazione esistenti

### `Menu/nome_ristorante.pdf`

- 30 documenti contenenti i menù di diversi ristoranti
- Ogni file include:
    - Descrizione dello chef
    - Licenze ottenute (nome della skill e livello raggiunto)
    - Menu con piatti (nome, descrizione, ingredienti, tecniche utilizzate)

### `Blogpost/nome_ristorante.html`

- Documenti in markdown con informazioni supplementari su alcuni ristoranti
- Contiene recensioni di due ristoranti con voto su scala 0-10 espresso in stelle

### `Misc/dish_mapping.json`

- Mappatura dei piatti in ID numerici progressivi
- Lista di circa 300 piatti con formato `"nome piatto": id_numerico`
- Necessario per generare l'output finale

## Analisi del codice

### `Licenze.ipynb`

- Notebook per l'estrazione JSON di tutte le licenze
- Utilizza `gpt-4o-mini`
- Estrae testo dai PDF utilizzando la libreria `fitz`

### `menu_to_json.py`

- Estrazione e creazione di JSON con informazioni dai menù
- Processa l'intera cartella menù (non fornita)
- Utilizza `gpt-4o-mini` con few-shot examples del formato JSON desiderato
- Estrae anche nome del ristorante e del pianeta in un JSON separato
- **Nota**: manca la funzione `parse_pdfs_folder` che sarebbe fondamentale

### `graph_construction.py`

- Configurazione per accedere al graph database (utilizza `from neo4j import GraphDatabase`)
- Popolamento del DB con:
    - Chef
    - Menù
    - Nomi ristoranti
    - Pianeti
- Aggiunge le distanze tra pianeti (da tabella simmetrica)
- Utilizza funzioni non riportate come `process_planet_data` per le query di popolamento del DB

### `graph_retrival.py`

- Importa `from langchain_neo4j import Neo4jGraph`
- Utilizza `gpt-4-o` (a differenza degli altri file)
- Manca la funzione `answer_questions` che sembra fondamentale
- Contiene prompt per generare query Cypher per interrogare il graph DB
- Include un altro prompt per query fuzzy
- Ritorna il risultato finale



NOTE
Non ci sono veri e propri agenti ne tantomeno true agency (l'agente decide da solo quando fermarsi come Claude Plays Pokemon). Semplicemente una call llm va da query a cypher e un'altra call interpreta il risultato della query (un pezzo di grafo). Il grande lavoro è di parsing (loro lo fanno tramite llm, gpt4o ma anche gemini2.5 è pauroso in questo)
Loro hanno estratto il testo dai pdf con fitz e da lì hanno dato il markdown ad un llm con dei few shots per farsi creare le entità e poi hanno caricato tutto sul graphdb manager (neo4j)

- MA LE FUNZIONI NON FORNITE (O ALMENO NON SU GITHUB VOI LE AVETE E LE AVETE ANALIZZATE?) No loro hanno visto solo github, il fatto che fosse una graph rag

- MA IL GRAFO PERCHE SI DICE VENIR FATTO DA LLM SE COSI NON E'? VIENE FATTO NEL FILE GRAPH_CONSTRUCTION. In realtà le entità in sé vengono *definite a mano* (oppure vengono fatte estrarre da llm guardando base di conoscenza e domande, queste ultime non sempre le hai in un caso reale). Dopodiché si passano al modello sia le entità definite sia i documenti per farsi estrarre tutte le istanze di ciascuna entità (come se ciclassi su chiavi di un json facendo il corrispettivo cypher di insert into)

- CHE SONO LE QUERY FUZZY? Sono delle query un po' più robuste (termine fuzzy è un po' usato in maniera colloquiale). La parte fuzzy si rifà sia ad uno dei parametri che prende in ingresso neo4j per il tipo di query. Sia al mapping tra i nomi delle ricette estratte e i numeri corrispondenti (a "pasta al forno lunare" corrisponde 14 ma pure a "pasta lunare al forno" deve corrispondere 14). In generale considera metriche di **string similarities** come Levenstein ed alignment (ciao e caio hanno distanza 4 per esempio, robe usate nel DNA)

- COME FUNZIONA CYPHER? Essenzialmente è sql per graph db

- Funzione answer questions? Cicla sulle domande e vada a trasformarle in query cypher, e andava con la generazione delle risposte

