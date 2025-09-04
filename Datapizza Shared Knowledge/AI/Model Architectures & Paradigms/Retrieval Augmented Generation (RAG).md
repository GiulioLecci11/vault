## Introduzione

La **RAG** combina il recupero di informazioni con la generazione di testo, permettendo ai modelli di linguaggio di accedere a conoscenze esterne aggiornate.

**Perché la RAG rimane importante:**

> "This observation challenges the common assumption that RAG might become obsolete with infinite context models. Chip argues that larger context windows don't necessarily solve the fundamental challenges RAG addresses, particularly noting that models often struggle with information buried in the middle of large context windows."

Anche con contesti infiniti, la RAG mantiene vantaggi in termini di precisione, controllo e gestione delle informazioni.

# Architettura RAG Classica

![[Pasted image 20250904091822.png]]

## Pipeline Completa

```
1. INGESTION: Documenti → Chunks → Embeddings → Vector Store
2. RETRIEVAL: Query → Search → Chunks Rilevanti
3. GENERATION: Query + Chunks → LLM → Risposta

```

## 1. Fase di Ingestion

### 1.1 Parsing dei Documenti

- **Tool**: AzureParser con AzureDocumentIntelligence
- **Scopo**: Estrazione testo strutturato da documenti complessi
- **Input**: PDF, DOCX, HTML, etc.
- **Output**: Testo pulito con metadati

### 1.2 Splitting in Chunks

```
Documento → Chunks con metadati
Metadati comuni:
- source_file: nome file originale
- source_page: numero pagina
- source_date: data documento
- document_type: tipo documento
- access_level: livello di accesso

```

**Considerazioni tecniche:**

- Dimensione chunk: 512-1024 token tipicamente
- Overlap: 50-100 token tra chunks adiacenti
- Idealmente, preservare contesto semantico

### 1.3 Embedding e Storage

```
Chunks → Embedding Model → Vector Representations → Qdrant

```

**Best practices:**

- **OpenAI embeddings**: Preferiti per minor appiattimento, tipicamente noi usiamo text-embedding-3-large
- **Vector Store**: Qdrant per performance e scalabilità
- **Upsert**: Update + Insert per aggiornamenti incrementali

## 2. Fase di Retrieval

### 2.1 Similarity Search

```
Query → Embedding → Similarity Search → Top-K Chunks

```

### 2.2 Filtering sui Metadati

```python
# Esempio filtering
filters = {
    "source_doc_type": ["report", "manual"],
    "source_date": {">=": "2024-01-01"},
    "access_level": {"<=": user.permission_level}
}
chunks = vector_store.search(query_embedding, filters=filters)

```

### 2.3 Reranking

```
Chunks Retrievati → Reranking Model → Chunks Riordinati

```

**Modelli reranking:**

- **Cohere Rerank**: Modello fine-tuned specifico
- **Cross-encoder**: Confronto query-documento diretto
- **Benefit**: Introduce intelligenza oltre similarity

## 3. Fase di Generation

### 3.1 Prompt Construction

```
Template:
"Usa queste informazioni per rispondere alla domanda:

CONTESTO:
{chunks_text}

DOMANDA: {user_query}

RISPOSTA:"

```

### 3.2 LLM Processing

- **Input**: Query + Chunks contestuali
- **Processing**: LLM sceglie informazioni rilevanti
- **Output**: Risposta basata su evidenze

# Tecniche Pre-Retrieval

### Query Rewriting

```
Query originale: "Come fare reset?"
Query riscritta: "Come fare reset della password nel sistema aziendale, considerando la conversazione precedente su problemi di login"

```

**Benefici:**

- Incorpora contesto conversazionale
- Disambigua query ambigue
- Migliora recall

### Query Expansion

```
Query input: "problemi rete"

Query generate:
1. "problemi di connessione di rete"
2. "errori di networking"
3. "disconnessioni internet"
4. "troubleshooting connettività"

→ Retrieve per ogni query → Merge risultati → Top-K finale

```

**Processo:**

1. LLM genera varianti query
2. Retrieval parallelo per ogni variante
3. Deduplicazione risultati
4. Scoring combinato per ranking finale

# Alternative per Multi-Hop Queries

### Limitazioni RAG Classica

- **Single-hop**: Una query → Un set di chunks
- **Cross-document reasoning**: Difficile collegare info tra documenti
- **Complex queries**: Domande che richiedono ragionamento multi-step

---

# RAG Agentica

```
Query → Agenti Specializzati → Aggregatore → Risposta

Esempio Allianz:
- Agente 1: Chunks su polizze auto
- Agente 2: Chunks su sinistri
- Agente 3: Chunks su procedure
- Aggregatore: Sintesi delle risposte parziali

```

**Vantaggi:**

- Parallelizzazione elaborazione
- Specializzazione per dominio
- Ragionamento multi-perspective

## Graph RAG

```
Tradizionale: Documenti → Chunks → Embeddings
Graph RAG: Documenti → Entities + Relations → Knowledge Graph + Embeddings

```

**Componenti:**

- **Entities**: Persone, luoghi, concetti, eventi
- **Relations**: Collegamenti semantici tra entities
- **Neo4j**: Database grafo per storage e query
- **Hybrid search**: Vector similarity + Graph traversal

**Benefits:**

- Schema flessibile e evolutivo
- Relazioni esplicite tra concetti
- Query complesse su multiple entities

## Hierarchical RAG

```
Struttura documento:
1. Introduzione
   1.1 Obiettivi
   1.2 Scope
2. Metodologia
   2.1 Approccio
   2.2 Tools

Chunk example:
"1 → 1.1 → Obiettivi: Il progetto mira a sviluppare..."

```

**Vantaggi:**

- Preserva struttura logica documento
- Context path per ogni chunk
- Navigazione gerarchica risultati

# Tecniche Avanzate per Embedding

### Reverse HyDE (Hypothetical Document Embedding)

```
Chunk input: "Procedura per configurare VPN aziendale..."

LLM genera domande:
1. "Come si configura la VPN?"
2. "Quali sono i passaggi per setup VPN?"
3. "Come risolvere problemi VPN?"
...10 domande totali

→ 1 chunk originale + 10 chunks con domande
→ Migliore matching per query utente

```

### Summary Embedding

```
Chunk lungo (1000+ token) → LLM Summary → Embedding separato

Benefici:
- Cattura concetti ad alto livello
- Riduce noise da dettagli
- Dual search: summary + full content

```

# Evaluation e Metriche

L'evaluation può essere fatta:

- a livello di chunk estratti
- a livello di testo generato

## 1. Evaluation a Livello di Chunks (Retrieval Quality)

### Hit Rate

Rileva se il sistema trova informazioni rilevanti.

**Esempio pratico:**

```
Query: "Come resettare la password del sistema CRM?"

Chunks recuperati:
1. ✅ "Procedura reset password CRM: vai su Impostazioni..."
2. ❌ "Storia del sistema CRM sviluppato nel 2019..."
3. ✅ "Troubleshooting login: se hai dimenticato la password..."
4. ❌ "Policy di sicurezza generale dell'azienda..."
5. ✅ "FAQ reset credenziali: contatta l'admin se..."

Hit Rate = 3 chunks rilevanti / 5 chunks totali = 60%

```

**Soglie tipiche:**

- **Eccellente**: >80% - Sistema trova quasi sempre info utili
- **Buono**: 60-80% - Maggioranza chunks rilevanti
- **Problematico**: <60% - Troppo noise nei risultati

### Precision e Recall

**Scenario: Knowledge Base aziendale con 1000 documenti**

Query: "Procedura per rimborso spese trasferta"

```
Chunks nel database: 10,000 totali
Chunks rilevanti per query: 8 (ground truth)
Chunks recuperati dal sistema: 10
Chunks rilevanti tra quelli recuperati: 6

Precision = 6/10 = 60% (di quello che recupero, quanto è utile?)
Recall = 6/8 = 75% (di quello che esiste, quanto riesco a trovare?)

```

**Interpretazione intuitiva:**

- **Alta Precision, Basso Recall**: Trova poche cose ma giuste → Conservativo
- **Bassa Precision, Alto Recall**: Trova tutto ma con molto rumore → Aggressivo
- **Ideale**: Bilanciamento alto-alto

### MRR (Mean Reciprocal Rank)

Rileva se il risultato migliore è in cima.

È importante anche tenere conto dell'ordinamento dei risultati, in quanto un LLM che riceve in input tanti chunks, rischia di perdersi (needle in the haystack) alcune informazioni centrali.

**Esempio:**

```
Query: "Policy ferie aziendali"

Risultati ordinati:
1. ❌ "Regolamento interno generale"
2. ❌ "Storia dell'azienda"
3. ✅ "Policy ferie e permessi" ← Primo risultato rilevante in posizione 3

Reciprocal Rank = 1/3 = 0.33

Su 100 query test:
- 40 query: primo risultato corretto (RR = 1.0)
- 30 query: secondo risultato corretto (RR = 0.5)
- 20 query: terzo risultato corretto (RR = 0.33)
- 10 query: nessun risultato rilevante (RR = 0.0)

MRR = (40×1.0 + 30×0.5 + 20×0.33 + 10×0.0) / 100 = 0.62

```

**Soglie pratiche:**

- **MRR > 0.8**: Utenti trovano subito quello che cercano
- **MRR 0.5-0.8**: Buono, ma si può migliorare
- **MRR < 0.5**: Frustrante per gli utenti

## 2. Evaluation a Livello di Testo Generato

### BLEU e ROUGE Score

**Esempio concreto:**

Query: "Come configurare la VPN aziendale?"

```
Ground Truth (risposta perfetta):
"Per configurare la VPN aziendale: 1) Scarica il client VPN dal portale IT, 2) Inserisci le credenziali fornite dall'amministratore, 3) Seleziona il server Italia-Milano, 4) Clicca Connetti."

RAG Output:
"Per impostare la VPN: 1) Scarica l'applicazione VPN dal sito interno, 2) Usa username e password ricevuti via email, 3) Scegli server Milano, 4) Premi il pulsante Connetti."

BLEU Score: 0.72 (sovrapposizione n-grammi)
ROUGE Score: 0.68 (overlap semantico)

```

**Interpretazione:**

- **BLEU/ROUGE > 0.7**: Risposta molto simile a quella ideale
- **0.4-0.7**: Buona qualità, contenuto corretto ma forma diversa
- **< 0.4**: Significative differenze o errori di contenuto

### Semantic Similarity

**Approccio moderno con embedding:**

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')

ground_truth = "Il sistema CRM permette di gestire contatti e opportunità di vendita"
rag_output = "La piattaforma CRM consente di amministrare clienti e prospect commerciali"

# Calcola embeddings
emb1 = model.encode([ground_truth])
emb2 = model.encode([rag_output])

# Cosine similarity
similarity = cosine_similarity(emb1, emb2)[0][0]
print(f"Semantic Similarity: {similarity:.3f}")  # Output: 0.847

```

**Vantaggi rispetto BLEU/ROUGE:**

- Cattura sinonimi ("contatti" ≈ "clienti")
- Comprende parafrasi semanticamente equivalenti
- Meno sensibile a variazioni stilistiche

### LLM as a Judge

Faccio valutare la similarità ad un llm `ground_truth` e `rag_output`, inserendoli nello `user_prompt`.

## 3. Evaluation End-to-End

### Human Evaluation

**Setup pratico:**

```
Test set: 100 query rappresentative
Annotatori: 3 esperti di dominio
Scala valutazione: 1-5

Per ogni risposta valutare:
1. Accuratezza: "È corretta?" (1=Sbagliata, 5=Perfetta)
2. Completezza: "Risponde tutto?" (1=Parziale, 5=Completa)
3. Chiarezza: "È comprensibile?" (1=Confusa, 5=Chiarissima)
4. Utilità: "Aiuta l'utente?" (1=Inutile, 5=Molto utile)

Risultati esempio:
- Accuratezza media: 4.2/5
- Completezza media: 3.8/5
- Chiarezza media: 4.1/5
- Utilità media: 4.0/5

```

## 4. Monitoring in Produzione

### Real-time Quality Metrics

**Dashboard di monitoraggio:**

```
Metriche Live (ultimo mese):
- Query totali: 15,247
- Hit rate chunks: 78% (↗️ +3%)
- Latenza media: 1.2s (↗️ +0.1s)
- Costo per query: $0.03 (↘️ -$0.005)
- Feedback negativo: 12% (↗️ +2%)

Alert attivi:
- Latenza > 2s per 5% query (last hour)
- Hit rate < 70% per query categoria "Tecnico"

```

### User Feedback Loop

**Sistema di feedback implicito:**

```
Segnali positivi:
- User clicca "Risposta utile"
- Nessuna query di follow-up entro 5min
- Tempo di lettura > 30 secondi
- Copia il testo della risposta

Segnali negativi:
- User clicca "Risposta inutile"
- Query simile richiesta subito dopo
- Abbandona pagina < 10 secondi
- Modifica significativamente la query

```

### A/B Testing

**Esempio test reale:**

```
Test: "Miglioramento Reranking"

Control (A): Similarity search standard
Variant (B): Similarity + Cohere Rerank

Metriche dopo 2 settimane:
                Control(A)  Variant(B)  Lift
Hit Rate:       74%         81%         +9.5%
User Rating:    3.4/5       3.8/5       +11.8%
Query Solved:   68%         74%         +8.8%
Cost per Query: $0.02       $0.035      +75%

Decisione: Deploy variant B (ROI positivo)
```

#dpKnowledge 