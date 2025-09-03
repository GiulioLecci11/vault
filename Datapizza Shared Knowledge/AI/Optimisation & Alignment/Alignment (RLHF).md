Usa il Reinforcement Learning from Human Feedback per **allineare il comportamento del modello alle preferenze umane**.

L'idea di base è che è difficile definire una loss function in grado di "valutare" un output testuale (nemmeno con bleu e rouge è semplice).

Perciò si definisce un modello di Reward (un LM) che è il più possibile allineato con il feedback umano (ritorna uno score per ogni output che riceve in input)

### A grandi linee
	
![[Pasted image 20250828101555.png]]
## Processo:

1. Umano classifica le risposte che vengono usate come training per il reward model.
    
2. Reward model $R$ che è **il più possibile allineato con l'evaluation umana**.
    
    $$ R : I(text)\rightarrow O(score) $$
    
1. Alleno il mio LLM (cercando di minimizzare gli scores del Reward Model) tramite Proximal Policy Optimization (PPO).
    
    Il processo è il seguente:
    
    1. La policy $P$ è un LM che viene allenato man mano. Esso genera una risposta
    
    $$ P:I(prompt)\rightarrow O(text) $$
    
    1. La risposta di $P$ viene valutata da $R$, si calcola una ricompensa e si aggiorna $P$ usando PPO per massimizzare la ricompensa.
        ![[Pasted image 20250828101621.png]]
        
2. Il modello finale (Instruct) sarà il policy model, quello di partenza non viene mai toccato
    

**Figura:**

```
Prompt --> LLM --> Risposta A/B --> Voto umano --> Reward model --> LLM (ottimizzato)
```

## Output:

- Instruct Models

## Tecniche alternative al RLHF

- **DPO (Direct Preference Optimization)**: ottimizza direttamente i log-likelihood usando coppie preferite.
- **Constitutional AI**: modelli guidati da principi scritti (es. etica, sicurezza).
- **RLAIF (Reinforcement Learning from AI Feedback)**: feedback generato da modelli invece che da umani.

#dpKnowledge 