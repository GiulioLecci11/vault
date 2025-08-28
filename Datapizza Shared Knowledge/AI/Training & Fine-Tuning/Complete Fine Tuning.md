Questa tecnica aggiorna **tutti i parametri** del modello. È la forma più potente ma anche la più costosa in termini computazionali; può comportare rischi come l’overfitting o il Catastrophic Forgetting, dove il modello dimentica le conoscenze apprese in precedenza.

## Pro

- Massima flessibilità e personalizzazione.

## Contro

- Rischio di overfitting.
- Richiede molta memoria e dati.

**Esempio:**

```python
from transformers import Trainer, TrainingArguments, AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("gpt2")
training_args = TrainingArguments(output_dir="./results", per_device_train_batch_size=2, num_train_epochs=1)

trainer = Trainer(model=model, args=training_args, train_dataset=my_dataset)
trainer.train()
```

**IMPORTANTE**

Esegui il fine-tuning su un sottoinsieme più piccolo del dataset completo per ridurre i tempi necessari.

I risultati non saranno buoni come quelli ottenuti con il fine-tuning sull'intero dataset, ma è utile per assicurarsi che tutto funzioni come previsto prima di impegnarsi nell'addestramento sul dataset completo.

#dpKnowledge 