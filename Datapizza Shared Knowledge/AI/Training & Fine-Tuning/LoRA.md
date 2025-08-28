Quando fai Fine Tuning vai a modificare la matrice dei pesi, quindi formalmente

$$ W_1=W_0+\triangle W $$

dove $W \in \mathbb{R}^{d_{out} \times d_{in}}$, ma spesso $\triangle W$ è molto grande da calcolare.

**LoRA** introduce due matrici a basso rango che agiscono come "correttori" e che approssimano $\triangle W$, i.e:

$$ ⁍ $$

dove

- $A \in \mathbb{R}^{r \times d_{in}}$
- $B \in \mathbb{R}^{d_{out} \times r}$
- $r \ll \min(d_{in}, d_{out})$ (quindi è molto più piccolo della dimensione originale)

La correzione è quindi una matrice di rango al più $r$.

## Pro

- Minimo uso di memoria.
- Possibilità di usare più adapter per compiti diversi.
- Non introduci latenza

## Output

- LLM base + matrici che approssimano le modifiche ai weights = LLM specializzato.

## Procedimento

### **PASSO 1: Inizializzazione (Interazione 0)**

- Partiamo da:
    - W pre-addestrato (ad esempio da un modello pre-addestrato)
    - Inizializziamo:
        - $A$ → sampling casuale da una [Distribuzione Gaussiana](https://www.notion.so/Distribuzione-Gaussiana-24e04d204fa28047932df7ef40347fb4?pvs=21) , con piccola varianza
        - $B$ → **spesso inizializzato a zeri** per partire “neutro”
- Quindi all’inizio: $W{\prime} = W + 0 \cdot A = W$

Il modello si comporta esattamente come il modello pre-addestrato.

---

### **PASSO 2: Prima interazione (Interazione 1)**

- Facciamo un forward pass sui dati.
- Calcoliamo la loss.
- Facciamo **backprop solo su** $A$ **e** $B$:
    - $W$ **non viene aggiornato** (frozen)
    - i gradienti vengono calcolati su $A$ e $B$

Aggiorniamo $A$ e $B$ con SGD/Adam o altro ottimizzatore.

Alla fine di questo step, A e B hanno subito un primo aggiornamento → molto piccolo.

$$

W{\prime} = W + B^{(1)} A^{(1)}

$$

e $B A$ inizia ad “aggiustare” $W$ per adattarsi ai nuovi dati.

---

### **PASSO 3: Interazione 2 (e successive)**

Ad ogni nuovo batch:

1. Facciamo forward pass con $y = f(W + B A, x)$
2. Calcoliamo loss.
3. Facciamo backward pass, calcolando gradienti solo su $A$ e $B$.
4. Aggiorniamo $A$ e $B$.

Questa procedura si ripete per **ogni batch/epoca**. Ad ogni interazione $t$:

$$ W{\prime} = W + B^{(t)} A^{(t)}

$$

e $B A$ progressivamente apprende **una correzione a basso rango** per il peso originale $W,$ per minimizzare la loss sul nuovo task.

---

### **PASSO FINALE: Inferenza**

Dopo il training:

- Usi $W{\prime} = W + B A$ per inferenza.
- Oppure **“fondi”** $B A$ **dentro** $W$ e poi usi solo $W{\prime}$ (se lo vuoi serializzare come una sola matrice).

## Dettagli

- Il **Rango matriciale** delle due matrici che introduci è un iperparametro che va a definire quanti pesi originali del modello vai a modificare. Maggiore è il rango maggiore è il numero di parametri che modifichi.

|Rank|7B|13B|70B|180B|
|---|---|---|---|---|
|1|0.00%|0.00%|0.00%|0.00%|
|2|0.01%|0.00%|0.00%|0.00%|
|8|0.02%|0.01%|0.01%|0.00%|
|16|0.04%|0.03%|0.01%|0.01%|
|512|1.22%|0.90%|0.39%|0.24%|
|1,024|2.45%|1.80%|0.77%|0.48%|
|8,192|19.58%|14.37%|6.19%|3.86%|

(es. $R=8$ su un modello $7B$ vai a modificare solo il $0.02\%$ dei parametri, ovvero $1M$ )

NB: empiricamente si usa un rango alto **sse** voglio che il modello impari un comportamento complesso oppure un comportamento contradditorio rispetto al suo training set.

- Si vuole APPROSSIMARE $\triangle W$ usando due matrici $A,B$ il cui prodotto è simile a $\triangle W$

$$ W_0 + \triangle W=W_0+BA $$
![[Pasted image 20250827111833.png]]
(es. se $\triangle W \in (200,200)$ ti bastano due matrici $A \in (2,200), B \in (200,2)$ e hai un risparmio)

- Non introduci mai latenza in quanto fai il merge dei pesi, quindi $W_0+BA$

![[Pasted image 20250827111846.png]]
## Codice

```python
from peft import get_peft_model, LoraConfig

peft_config = LoraConfig(task_type="CAUSAL_LM", r=8, lora_alpha=32)
model = get_peft_model(base_model, peft_config)

```

#dpKnowledge 