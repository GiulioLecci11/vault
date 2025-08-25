Quando trainiamo un modello di linguaggio, questo impara la distribuzione dei suoi training data.

La **cross entropy** di un certo modello su un certo dataset indica quanto è difficile per quel modello predire il prossimo token all'interno di quel dataset. (è un'approssimazione dell'[Entropy](https://www.notion.so/Entropy-24e04d204fa2808493fcc09d94c5657a?pvs=21) dei suoi training data.)

La cross entropy di un modello dipende da:

- la predicibilità dei training data, misurata tramite l'entropia di tali dati
- come la distribuzione catturata dal modello diverge dalla vera distribuzione presente nei training data.

## Definizione Matematica

La **cross entropy** tra una distribuzione di probabilità vera $p$ e una distribuzione predetta $q$ è definita come:

$$

H(p,q) = -∑ p(x) log(q(x))

$$

Nel caso di classificazione binaria:

$$

H(p,q) = -[y log(ŷ) + (1-y) log(1-ŷ)]

$$

Dove:

- $y$ è l'etichetta vera (0 o 1)
- $\hat{y}$ è la probabilità predetta dal modello

## Esempi Pratici

### 1. Classificazione di Immagini

Un modello che classifica immagini come "gatto" o "cane":

- **Predizione perfetta**: Se l'immagine è un gatto (y=1) e il modello predice probabilità 1.0 per "gatto", la cross entropy è 0
- **Predizione errata**: Se l'immagine è un gatto (y=1) ma il modello predice probabilità 0.1 per "gatto", la cross entropy è alta: -log(0.1) ≈ 2.3

### 2. Language Model

Un modello di linguaggio che predice la prossima parola:

- **Frase**: "Il gatto dorme sul..."
- **Parola corretta**: "letto"
- **Predizione modello**: 70% "letto", 20% "divano", 10% "tavolo"
- La cross entropy sarà: -log(0.7) ≈ 0.36

## Relazioni con Altri Concetti

### Cross Entropy vs Entropy

- **[[Entropy]]**: $H(p) = -∑ p(x) log(p(x))$ - misura l'incertezza intrinseca dei dati
- **Cross Entropy**: $H(p,q) = -∑ p(x) log q(x)$ - misura quanto bene il modello approssima la distribuzione vera

## Utilizzo Pratico

### Come Loss Function

La cross entropy è ampiamente usata come **funzione di perdita** perché:

- È **differenziabile** - permette l'ottimizzazione tramite gradient descent
- **Penalizza fortemente** predizioni molto sbagliate
- Converge più velocemente rispetto ad altre metriche come l'errore quadratico medio

### Interpretazione dei Valori

- **Cross entropy bassa** (< 1.0): Il modello predice bene
- **Cross entropy alta** (> 3.0): Il modello è molto confuso
- **Cross entropy = entropy dei dati**: Il modello non ha imparato nulla (predice random)

## Applicazioni

- **Neural Networks**: Loss function principale per classificazione
- **Language Models**: Valutazione della perplexity
- **Information Theory**: Misura di compressibilità dei dati
- **Reinforcement Learning**: Ottimizzazione delle policy

#dpKnowledge 