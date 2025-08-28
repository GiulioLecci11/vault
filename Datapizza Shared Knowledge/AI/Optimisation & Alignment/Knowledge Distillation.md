# Intuizione

La distillazione consiste nell'addestrare un modello più piccolo (student) per imitare il comportamento di un modello più grande (teacher), usando input e output del teacher.

Benefici: meno risorse, più efficienza, stessa conoscenza.

## Concetto Matematico

La **Knowledge Distillation** usa la **soft loss function** che combina:

$$

L_{total} = \alpha L_{hard} + (1-\alpha) L_{soft}

$$

Dove:

- $L_{hard}$ = cross-entropy con le etichette vere
- $L_{soft}$ = cross-entropy con le predizioni del teacher (addolcite)
- $\alpha$ = parametro di bilanciamento (tipicamente 0.1-0.5)

### Temperature Scaling

Le predizioni del teacher vengono "addolcite" usando la **temperature** $T$:

$$

p_i = \frac{e^{z_i/T}}{\sum_j e^{z_j/T}}

$$

- $T = 1$: predizioni normali
- $T > 1$: predizioni più "morbide" (distribuisce probabilità)
- $T$ tipicamente tra 3-10

## Esempi Pratici

### 1. Classificazione di Immagini

**Teacher**: ResNet-152 (60M parametri) **Student**: MobileNet (4M parametri)

```
Input: Foto di un gatto
Teacher output: [gatto: 0.9, cane: 0.08, uccello: 0.02]
Student impara da: sia l'etichetta vera "gatto" che la distribuzione del teacher
```

Lo student impara che "cane" è più simile a "gatto" di quanto lo sia "uccello".

### 2. Language Models

**Teacher**: GPT-3 (175B parametri)

**Student**: DistilBERT (66M parametri)

```
Input: "Il tempo oggi è molto..."
Teacher: [bello: 0.4, brutto: 0.3, nuvoloso: 0.2, piovoso: 0.1]
Student: impara questa distribuzione di probabilità, non solo la parola più probabile
```

### 3. Chatbot Aziendale

**Teacher**: Modello complesso addestrato su milioni di conversazioni **Student**: Modello leggero per deployment mobile

Il teacher fornisce risposte "sfumate" che aiutano lo student a capire le sottili differenze tra risposte simili.

## Tipi di Distillation

### 1. **Response-based Distillation**

Lo student imita solo l'output finale del teacher.

### 2. **Feature-based Distillation**

Lo student imita anche i layer interni del teacher:

$$

L_{feature} = ||f_{student}(x) - f_{teacher}(x)||^2

$$

### 3. **Attention Transfer**

Trasferisce i pattern di attenzione del teacher allo student.

## Vantaggi Concreti

### Efficienza Computazionale

- **Teacher**: 1000ms per predizione
- **Student**: 50ms per predizione
- **Accuratezza**: 95% vs 97% (perdita minima)

### Deployment

- **Mobile**: Modelli student possono girare su smartphone
- **Edge computing**: Meno memoria e energia richiesta
- **Real-time**: Latenza ridotta per applicazioni critiche

## Limitazioni

- **Dipendenza dal teacher**: Se il teacher ha bias, li trasmette
- **Task specificity**: Difficile generalizzare a domini molto diversi
- **Dark knowledge**: Non sempre tutto il "sapere" del teacher si trasferisce

#dpKnowledge 