# Standard HyDE

```
Query → Generate Hypothetical Document → Embed Document → Search
```

# Reverse HyDE

```
Document Chunk → Generate Hypothetical Questions → Embed Questions → Search
```

## Implementazione

### 1. Fase di Preprocessing (Indexing)

```python
def reverse_hyde_preprocessing(document_chunk):
    """
    Per ogni chunk, genera domande ipotetiche che potrebbero
    portare a quel chunk come risposta
    """

    prompt = f"""
    Dato questo documento:
    {document_chunk}

    Genera 5-10 domande specifiche che un utente potrebbe fare
    per ottenere queste informazioni:
    """

    questions = llm.generate(prompt)

    # Embedding delle domande generate
    question_embeddings = embedding_model.encode(questions)

    return {
        "chunk": document_chunk,
        "hypothetical_questions": questions,
        "question_embeddings": question_embeddings
    }

```

### 2. Fase di Retrieval

```python
def reverse_hyde_search(user_query):
    """
    Cerca la query dell'utente contro le domande ipotetiche
    invece che contro i chunks originali
    """

    query_embedding = embedding_model.encode(user_query)

    # Cerca contro tutte le domande ipotetiche
    similar_questions = vector_store.search(
        query_embedding,
        field="question_embeddings"
    )

    # Recupera i chunks associati alle domande più simili
    relevant_chunks = [q.associated_chunk for q in similar_questions]

    return relevant_chunks

```

## Esempio

### Documento Originale

```
"Per resettare la password del CRM: 1) Vai su Impostazioni,
2) Clicca Sicurezza, 3) Seleziona Reset Password, 4) Inserisci
la nuova password, 5) Conferma via email."

```

### Domande Ipotetiche Generate

```
1. "Come posso cambiare la mia password del CRM?"
2. "Dove trovo l'opzione per reset password?"
3. "Ho dimenticato la password del sistema, come la cambio?"
4. "Quali sono i passaggi per modificare le credenziali?"
5. "Come si accede alle impostazioni di sicurezza?"

```

### Retrieval Process

```
User Query: "Non ricordo la password del sistema"

Matching con domande ipotetiche:
- Domanda 3: "Ho dimenticato la password..." → Similarity: 0.92 ✅
- Domanda 1: "Come posso cambiare..." → Similarity: 0.78 ✅
- Domanda 4: "Quali sono i passaggi..." → Similarity: 0.65 ✅

→ Recupera il chunk associato a queste domande

```

## Vantaggi

### 1. Bridging Query-Document Gap

```
Problema: User query stilisticamente diversa da documento

User: "Ho perso l'accesso al sistema"
Document: "Procedura reset credenziali sistema CRM"

→ Similarity bassa con embedding tradizionale

Con Reverse HyDE:
Generated Q: "Ho dimenticato la password, come recupero l'accesso?"
→ Similarity alta con user query

```

### 2. Multiple Query Perspectives

```
Un documento può essere trovato attraverso diverse formulazioni:

Documento: "Procedura rimborso spese trasferta"

Domande generate:
- "Come richiedere rimborso per viaggi di lavoro?"
- "Dove invio le ricevute delle spese?"
- "Quali documenti servono per il rimborso?"
- "Come viene processato il rimborso trasferte?"

→ Aumenta recall per diverse varianti di query

```

## Confronto con Tecniche Simili

|Tecnica|Processo|Quando Usare|
|---|---|---|
|**Standard Retrieval**|Query → Embed → Search Chunks|Query e documenti simili stilisticamente|
|**HyDE**|Query → Generate Doc → Embed → Search|Query vaghe, serve espansione|
|**Reverse HyDE**|Chunk → Generate Q → Embed → Search against Q|Gap stilistico query-documento|
|**Query Expansion**|Query → Generate Variants → Search|Migliorare recall|

## Implementazione Pratica

### Vector Store Setup

```python
# Traditional approach
vector_store.add_documents(
    documents=chunks,
    embeddings=embed_model.encode(chunks)
)

# Reverse HyDE approach
for chunk in chunks:
    questions = generate_questions(chunk)
    vector_store.add_documents(
        documents=questions,
        embeddings=embed_model.encode(questions),
        metadata={"source_chunk": chunk}
    )

```

### Hybrid Approach

```python
def hybrid_search(query):
    # Traditional dense retrieval
    chunk_results = search_chunks(query)

    # Reverse HyDE retrieval
    question_results = search_questions(query)

    # Combine and rerank
    combined = merge_results(chunk_results, question_results)
    return rerank(query, combined)

```

## Limitazioni e Considerazioni

### 1. Computational Cost

```
Standard: 1 embedding per chunk
Reverse HyDE: 5-10 embeddings per chunk (per le domande)

→ Storage requirements aumentano 5-10x
→ Index build time aumenta significativamente

```

### 2. Quality Dependency

```
La qualità dipende dalla capacità dell'LLM di generare
domande rappresentative e diverse.

Domande generate male → Retrieval peggiore

```

### 3. Domain Specificity

```
Funziona meglio su:
- FAQ, documentazione tecnica, procedure
- Domini con query patterns prevedibili

Meno efficace su:
- Contenuto molto tecnico/specializzato
- Documenti con poche query patterns naturali

```

## Best Practices

### 1. Question Generation

```python
prompt = f"""
Generate diverse questions that users might ask to find this information.
Include:
- Direct questions
- Problem-based queries
- Procedural questions
- Troubleshooting scenarios

Document: {chunk}
Questions:
"""

```

### 2. Quality Control

```python
def filter_questions(questions, chunk):
    # Rimuovi domande troppo generiche
    questions = [q for q in questions if len(q.split()) > 5]

    # Verifica relevance con chunk originale
    relevance_scores = compute_relevance(questions, chunk)
    questions = [q for q, score in zip(questions, relevance_scores)
                if score > 0.7]

    return questions

```

#dpKnowledge 