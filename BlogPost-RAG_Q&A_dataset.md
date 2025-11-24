# **Riassunto tecnico del blogpost**

Il post descrive **come progettare un dataset avanzato per valutare sistemi RAG (Retrieval-Augmented Generation)**, con focus su domande multi-hop e wide. L’obiettivo è costruire dataset che riflettano difficoltà reali e non semplificate come quelle presenti in molti benchmark pubblici.

---

## **Perché servono dataset ad hoc**

I benchmark pubblici più diffusi (LegalBench, MultiHop-RAG, LoCoMo) non sempre rispecchiano i casi d’uso reali dei clienti. Due problemi principali:

1. **Assenza di multi-hop complessi**, che richiedono più step di reasoning.
2. **Schemi non portabili**, spesso chunk-based o con annotazioni non granulari.

Per valutare bene un sistema RAG servono dataset che:

* includano ragionamento a catena,
* siano indipendenti dalla strategia di chunking,
* offrano riferimenti riproducibili nel testo originale,
* abbiano domande di difficoltà controllata.

---

### 📌 **Che cosa hanno creato, esattamente?**

Hanno creato un **dataset di Q&A** (domande, risposte ed evidenze testuali) da usare *per valutare* la qualità di un sistema RAG — non la knowledge base stessa.

Ci sono due "tipi" di dataset in gioco, ma il blogpost si concentra solo sul secondo:

---

# ✅ **1. La Knowledge Base (KB) → i documenti sorgente**

Questa *non* è il contributo principale del blogpost.
È semplicemente il **corpus originale** che una RAG dovrebbe usare per rispondere.

Nel caso del dataset pubblico, si tratta di:

* **20 file markdown** estratti dal **D&D SRD 5.2.1**

Questi file rappresentano *le regole di D&D*.

**Non** hanno creato questo contenuto: l’hanno solo normalizzato e preparato in markdown.

---

# ✅ **2. Il dataset di *valutazione* → domande, risposte e span**

È questo **il dataset reale che loro hanno progettato**.

Contiene:

### 🟦 **Domande**

* Easy → generate automaticamente su singoli documenti
* Medium → scritte da esperti (multi-hop e wide)

### 🟩 **Risposte**

Generate tramite:

* LLM + Claude Skills (per multi-hop)
* LLM Retriever (per domande wide)

### 🟧 **Passaggi/Evidenze**

Per ogni domanda, ci sono:

* i documenti da cui è tratta la risposta,
* gli **intervalli di testo (start_char, end_char)**,
* lo **span estratto**.

Queste evidenze servono per verificare se una RAG recupera le parti giuste di testo.

💡 **È qui l’innovazione: la struttura char-based rende tutto chunk-agnostic.**

---

# 🎯 **In breve: cosa hanno creato?**

👉 **Un dataset Q&A di evaluation**, NON la knowledge base.
Contiene:

* **domande**
* **risposte corrette**
* **evidenze (span) per verificare retrieval e reasoning**

La KB è solo il materiale di partenza su cui costruiscono le domande.

---

# 📌 Perché serve questo dataset?

Per testare oggettivamente sistemi RAG su:

* multi-hop complessi,
* domande che coinvolgono più documenti,
* recupero di span testuali corretti,
* reasoning basato su fonti reali.

---

# 🔍 Se vuoi un’analogia:

* La **KB** = il libro di testo.
* Il **dataset di evaluation** = l’esame con domande, risposte corrette e pagine da cui arrivano.

Il blogpost parla dell'**esame**, non del libro.

---

## **La soluzione: dataset interni + uno pubblico (D&D SRD 5.2.1)**

Il team ha creato:

* due dataset interni (domini coperti da NDA),
* un dataset **pubblico** basato su D&D SRD 5.2.1,
* tutti con struttura **char-based** (chunk-agnostic).

### **Caratteristiche della struttura**

Ogni entry contiene:

* ID
* domanda
* risposta
* passaggi/evidenze (ognuno con documento, start_char, end_char, contenuto estratto)

La struttura **char-based** permette:

* riproducibilità,
* indipendenza dal chunking,
* verifica precisa degli span usati dal modello.

---

## **Generazione delle domande**

### **1. Livello Easy**

Automatica:

* si prende un documento markdown casuale,
* si chiede a un LLM di generare una domanda puntuale su quel documento,
* si evitano duplicati passando le domande già create finora.

Limite: **non genera domande cross-documento**.

### **2. Livello Medium**

Manuale + assistita:

* interviene un esperto per costruire domande **multi-hop** o **wide**,
* le domande vengono scritte a mano perché l’intera KB nel prompt genera domande troppo facili.

Domini usati:

* D&D (flavour semplice e noto),
* due dataset proprietari.

---

## **Generazione delle risposte e delle evidenze**

### **Easy**

Pipeline automatizzata:

1. Per ogni domanda si passa:

   * la domanda,
   * il singolo documento di riferimento.
2. LLM genera risposta e passaggi.
3. L’esperto valuta (accetta, corregge o scarta).

### **Medium**

Richiedono molta più complessità. Due categorie:

---

## **Medium Multi-hop → uso delle Claude Skills**

Per i multi-hop complessi è stata costruita una **Claude Skill custom** composta da:

* un manuale sintetico sull'organizzazione della KB,
* l’intera KB in markdown,
* due tool Python:

  * ricerca di pattern testuali,
  * espansione del contesto attorno ai match.

### **Workflow della Skill**

1. Accesso all’overview della KB.
2. Ricerca iterativa nei documenti:

   * pattern,
   * documenti collegati,
   * contesti ampliati.
3. Aggregazione dei risultati.
4. Produzione di risposta + passaggi dettagliati.

**Punti di forza:** reasoning multi-hop molto robusto.
**Problema:** costo medio elevato (~2$ a domanda, picchi a 11$).

---

## **Medium Wide → uso di LLM Retriever (stile LlamaIndex)**

Per le domande “wide”, che richiedono aggregazione da molti documenti ma non una catena logica intricata:

### **Pipeline**

1. Esecuzione in parallelo di un LLM per ogni documento → produce passaggi candidati.
2. Un LLM aggregatore combina le evidenze e formula la risposta finale.

**Punti di forza:** più economico delle Claude Skills sulle wide.
**Limiti:** inefficace sui multi-hop (un solo passaggio di estrazione).

---

## **Controllo qualità**

Ogni item passa tre step:

1. **Validazione semantica**
   La risposta è corretta e completa?

2. **Validazione span**
   Gli start_char e end_char corrispondono ai file originali?

3. **Rilevanza e difficoltà**
   La domanda rispecchia veramente la categoria easy/medium?

Domande ambigue o generiche vengono eliminate.

---

## **Dataset pubblico: D&D SRD 5.2.1**

* 56 domande (25 easy, 31 medium)
* 20 documenti markdown
* Domande su regole e meccaniche del gioco
* Disponibile su GitHub e HuggingFace

---

## **Limiti attuali e futuri sviluppi**

1. **Passaggi obbligatori vs opzionali**
   Oggi non differenziati → penalizza sistemi che trovano percorsi alternativi.

2. **RAG agentica avanzata**
   Si vuole testare pipeline di deep research multi-step.

3. **Domande hard**
   Un terzo livello che combini multi-hop + wide insieme.

4. **Multimodalità**
   Char-based non copre ancora immagini e grafici.

---

## **Conclusione**

Il blogpost presenta un metodo pratico e riproducibile per creare dataset di evaluation per RAG:

* char-based,
* con difficoltà controllata,
* generazione semi-automatizzata,
* strumenti specializzati per multi-hop (Claude Skill) e wide (LLM Retriever).

Il dataset pubblico D&D SRD 5.2.1 è già disponibile e mira a standardizzare e rendere trasparente la valutazione delle pipeline RAG.
