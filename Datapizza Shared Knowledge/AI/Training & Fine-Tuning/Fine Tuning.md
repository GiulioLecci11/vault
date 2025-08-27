
Il fine tuning è il processo di addestramento aggiuntivo di un modello linguistico pre-addestrato su un dataset specifico per specializzarlo in un determinato compito o dominio.

**SUPERVISED DATA** : nel FineTuning i dati sono sempre labelled `<input, output>`.

## Cosa modifica

|Nome|Cosa viene modificato|
|---|---|
|[Complete Fine Tuning](https://www.notion.so/Complete-Fine-Tuning-24e04d204fa280bb86d4d9c79c09f6c8?pvs=21)|Tutti i parametri del modello|
|[PEFT](https://www.notion.so/PEFT-24e04d204fa28025880ec3ec1b6870d8?pvs=21)|Solo una parte dei parametri, utilizzando varie tecniche di PEFT|
|Half Fine Tuning|Metà dei parametri del modello|

**Quando si usa il Fine Tuning?**

- Quando few-shot learning non è sufficiente.
- Per migliorare la performance su task aziendali specifici.

### Training samples

Nel caso di llm, per fare Fine Tuning hai bisogno di un numero di esempi che sia proporzionale al numero di parametri del modello.

$$ N_{\text{samples}}\propto k* N_{\text{trained parameters}} $$

Tipicamente per task NLP, $k\in[10,100]$

### Pipeline
![[Pasted image 20250827111202.png]]

1. [Data Preparation](https://www.notion.so/Data-Preparation-24e04d204fa2805c9892f135c1490f97?pvs=21) : fase in cui si selezionano i dati da dare in pasto al modello.
2. Model Initialisation: fase in cui scarico il mio pretrained model in e creo l'environment per fare fine-tuning.
3. Training Setup: decidere l'hw da usare e quali hyperparameters scegliere.
4. Selection of FineTuning Techniques

#dpKnowledge 