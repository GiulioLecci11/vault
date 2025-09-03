Architettura dove solo una parte (un sottoinsieme di esperti) del modello viene attivata per ogni input. Riduce il costo computazionale mantenendo alte prestazioni.

Router decide quali "esperti" attivare.

## Analogia Semplice

Immagina un ospedale con medici specializzati:

- **Paziente con mal di testa** → Il coordinatore lo manda dal neurologo
- **Paziente con frattura** → Il coordinatore lo manda dall'ortopedico
- **Paziente con febbre** → Il coordinatore lo manda dall'internista

Non serve che tutti i medici visitino ogni paziente - solo quelli rilevanti si attivano.

## Come Funziona

### 1. Struttura Base

```
Input → Router (Gating Network) → Seleziona Esperti → Output
```

### 2. Componenti Principali

**Router/Gateway:**

- Analizza l'input
- Decide quali esperti attivare
- Assegna pesi di importanza

**Esperti:**

- Reti neurali specializzate
- Ognuna eccelle in un dominio specifico
- Solo alcune si attivano per input

### 3. Processo Step-by-Step

```
1. Input: "Traduci: Hello world"
2. Router analizza: "È una traduzione dall'inglese"
3. Attiva: Esperto-Traduzione-Inglese (peso: 0.8)
4. Attiva: Esperto-Lingue-Europee (peso: 0.2)
5. Output combinato: "Ciao mondo"
```

## Esempi Pratici

### Modello di Linguaggio

```
Input: "Scrivi codice Python per ordinare una lista"

Router identifica: "Programmazione + Python"
→ Attiva: Esperto-Programmazione (70%)
→ Attiva: Esperto-Python (30%)
→ Ignora: Esperto-Letteratura, Esperto-Matematica
```

### Classificazione Immagini

```
Immagine: Foto di un gatto

Router identifica: "Animale + Mammifero + Domestico"
→ Attiva: Esperto-Animali (80%)
→ Attiva: Esperto-Forme-Organiche (20%)
→ Ignora: Esperto-Veicoli, Esperto-Edifici
```

## Vantaggi Concreti

### Efficienza Computazionale

- **Modello tradizionale**: Usa 100% dei parametri per ogni input
- **Mixture of Experts**: Usa solo 10-20% dei parametri per input
- **Risultato**: Stessa qualità con 5-10x meno calcoli

### Scalabilità

```
Aggiungere nuova capacità:
- Tradizionale: Riaddestra tutto il modello
- MoE: Aggiungi nuovo esperto specializzato
```

### Specializzazione

Ogni esperto diventa molto bravo nel suo dominio specifico, meglio di un modello generalista.

## Formule Base

### Router Decision

$$ g_i(x) = \text{softmax}(W_g \cdot x)_i $$

Dove:

- $g_i(x)$ = probabilità di attivare l'esperto i
- $W_g$ = parametri del router
- $x$ = input

### Output Finale

$$ y = \sum_{i=1}^{n} g_i(x) \cdot E_i(x) $$

Dove:

- $E_i(x)$ = output dell'esperto $i$
- La somma è pesata dalle decisioni del router

## Problemi e Soluzioni

### Load Balancing

**Problema**: Alcuni esperti vengono usati troppo, altri mai. **Soluzione**: Auxiliary loss che penalizza lo squilibrio.

### Training Instability

**Problema**: Il router può convergere male. **Soluzione**: Gradual expert activation durante training.

#dpKnowledge 