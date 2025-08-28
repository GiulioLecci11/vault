PPO serve per addestrare una policy (cioè un modello generativo, come GPT) a massimizzare una ricompensa, mantenendo il comportamento vicino alla policy originale (per evitare instabilità o degenerazioni del linguaggio).

## Componenti principali

- Policy: il modello (GPT, ad esempio), che genera una risposta dato un prompt.

$$

P:I(prompt)\rightarrow O(text) $$

- **Reward model**: valuta la qualità della risposta generata.

$$R : I(text)\rightarrow O(score)$$

- **Old policy**: la versione del modello prima dell’aggiornamento (usata per confronto). $P,P'$
- **Clip range**: un parametro che limita quanto può cambiare la nuova policy rispetto alla vecchia, per evitare aggiornamenti troppo grandi.

---

## **Procedimento**

### 1. **Raccolta dei dati**

- $P$ genera una risposta a partire da un prompt.
- $R$ assegna un punteggio a quella risposta (es. 7.5/10).
- Si salva: `prompt`, `risposta`, `reward`, `log-probabilità` della risposta sotto la **vecchia policy**.

---

### 2. **Definizione della ricompensa vantaggiosa (Advantage)**

- Si calcola quanto è meglio del previsto la risposta:
    
    $$ A_t = R_t - V(s_t)
    
    $$
    
    dove:
    
    - $R_t$ è il reward effettivo della risposta allo stato $t$.
    - $V(s_t)$ è il valore stimato della policy per quel prompt (stima dell’atteso) allo stato $t$. Nella pratica viene calcolato da una rete neurale che genera uno score (dettagli complicati).

---

IMPORTANTE: per capire i prossimi passaggi nota bene che

- $\pi_\theta$ è il modello con parametri $\theta$
- Lo **stato** $s_t$ è la sequenza di parole (o token) generate finora.
- L’**azione** $a_t$ è il **prossimo token** scelto.
- La distribuzione di probabilità è quella di $P$ nel generare il token successivo.

Esempio:

1. $s_t=$ “Il clima”
2. Probabilità del modello:

```
Token:      sta     è     sarà   cambia
Prob:       0.35   0.25   0.15    0.10

```

1. $a_t =$ "sta" perchè $\pi_\theta(a_t \mid s_t) = 0.35$ che è la probabilità maggiore

### 3. **Obiettivo di ottimizzazione (loss function)**

PPO massimizza la seguente funzione obiettivo:

$$ L^{CLIP}(\theta) = \mathbb{E}_t \left[ \min \left( r_t(\theta) A_t, \text{clip}(r_t(\theta), 1 - \epsilon, 1 + \epsilon) A_t \right) \right]

$$

dove:

- $r_t(\theta) = \frac{\pi_\theta(a_t | s_t)}{\pi_{\text{old}}(a_t | s_t)}$ è il rapporto tra la probabilità della risposta nella nuova vs vecchia policy.
- $\epsilon$ iperparametro (es. 0.2) che limita quanto può cambiare $r_t$
- Se il rapporto cambia troppo → viene “clippato” per sicurezza.

Effetto: si aggiorna solo se il miglioramento è stabile e non troppo drastico.

---

### 4. Aggiornamento dei pesi

- Si calcola il gradiente della loss.
- Si fa backpropagation per aggiornare i pesi del modello. Questo processo si ripete per più batch, usando le risposte raccolte.

---

## Perché PPO è utile in RLHF?

- Stabilità: evita che il modello si “rompa” ottimizzando troppo aggressivamente.
- Efficienza: richiede meno interazioni ambiente-policy rispetto ad altri algoritmi.
- Controllo: mantiene il modello vicino alla distribuzione originale (quella ottenuta da SFT), evitando risposte artificiose o fuori tema.

---

## Esempio pratico

### Step 0 – Setup

```python
from transformers import GPT2Tokenizer, GPT2LMHeadModel
import torch
import torch.nn.functional as F
# Inizializza tokenizer e due policy (vecchia e nuova)
tokenizer = GPT2Tokenizer.from_pretrained("gpt2")
policy_old = GPT2LMHeadModel.from_pretrained("gpt2")
policy_new = GPT2LMHeadModel.from_pretrained("gpt2")  # clone da ottimizzare
# Modalità di esecuzione
policy_old.eval()
policy_new.train()  # la nuova policy verrà aggiornata
# Prompt di input
prompt = "Why is the sky blue?"
inputs = tokenizer(prompt, return_tensors="pt")

```

---

### Step 1 – Generazione della risposta

```python
# Genera una risposta dalla nuova policy
with torch.no_grad():
    output = policy_new.generate(**inputs, max_new_tokens=10, do_sample=True)
    generated_text = tokenizer.decode(output[0])
    print("Generated:", generated_text)

```

Esempio di output:

```
Generated: Why is the sky blue? Because sunlight is scattered

```

---

### Step 2 – Calcolo delle log-probabilità token per token

```python
def get_logprobs(model, input_ids):
    with torch.no_grad():
        outputs = model(input_ids)
        logits = outputs.logits[:, :-1, :]  # Rimuovi l'ultimo token (no target)
        target_ids = input_ids[:, 1:]       # Token target da prevedere
        logprobs = F.log_softmax(logits, dim=-1)
        selected = logprobs.gather(2, target_ids.unsqueeze(-1)).squeeze(-1)
        return selected  # log-probabilità dei token effettivamente generati
# Ricostruisci input completo (prompt + risposta)
full_input = tokenizer(generated_text, return_tensors="pt").input_ids
# Calcola le log-probabilità da entrambi i modelli
logprobs_old = get_logprobs(policy_old, full_input)
logprobs_new = get_logprobs(policy_new, full_input)
# Calcola il ratio tra nuova e vecchia policy
ratios = torch.exp(logprobs_new - logprobs_old)  # r_t(θ)

```

---

### Step 3 – Simulazione reward e calcolo vantaggio

```python
# Reward simulato (in un caso reale proviene da un reward model)
reward = torch.tensor(7.5)
# Valore baseline (in pratica sarebbe un value head, qui è semplificato)
baseline_value = torch.tensor(6.0)
# Calcolo del vantaggio A_t
advantage = reward - baseline_value

```

---

### Step 4 – Calcolo della PPO loss clippata

```python
epsilon = 0.2
advantage = advantage.detach()
# Applica clipping al ratio
clipped_ratios = torch.clamp(ratios, 1 - epsilon, 1 + epsilon)
# Loss PPO per token
loss_per_token = -torch.min(ratios * advantage, clipped_ratios * advantage)
# Loss finale media sulla sequenza
ppo_loss = loss_per_token.mean()
print("PPO loss:", ppo_loss.item())

```

---

### Step 5 – Backpropagation

```python
# Backpropagazione del gradiente
ppo_loss.backward()
# Qui normalmente aggiungeresti un ottimizzatore tipo Adam:
# optimizer.step()

```

### Riepilogo

1. Generazione risposta con la nuova policy
2. Calcolo log-probabilità per ogni token (old vs new)
3. Simulazione reward e calcolo vantaggio
4. Applicazione della PPO loss clippata
5. Backpropagation per aggiornare la nuova policy

#dpKnowledge 