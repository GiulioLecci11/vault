## 1. Data Collection

Il primo passo nella preparazione dei dati è la raccolta da diverse fonti, come file CSV, pagine web, database SQL, storage S3, ecc. Python offre diverse librerie per raccogliere i dati in modo efficiente e accurato. (Vedi Tabella 3.1 per i formati e librerie più comuni.)

## 2. Data Preprocessing and Formatting

La pre-elaborazione e la formattazione dei dati sono fondamentali per ottenere dati di alta qualità per il fine-tuning. Questa fase include:

- Pulizia dei dati
- Gestione dei valori mancanti
- Formattazione secondo i requisiti specifici

### 2.1 Handling Data Imbalance

Gestire dataset sbilanciati è cruciale per ottenere performance bilanciate tra le classi. Tecniche e strategie comuni:

1. **Over-sampling e Under-sampling**
    
    Tecniche come SMOTE (Synthetic Minority Over-sampling Technique) generano esempi sintetici per bilanciare le classi.
    
    **Libreria Python:** [imbalanced-learn](https://imbalanced-learn.org/)
    
    **Descrizione:** Offre metodi per trattare dataset sbilanciati, inclusi tecniche di oversampling.
    
2. **Adjusting Loss Function**
    
    Modifica della funzione di loss per dare più peso alla classe minoritaria, impostando i pesi in modo inversamente proporzionale alla frequenza delle classi.
    
3. **Focal Loss**
    
    Variante della cross-entropy che riduce il peso degli esempi facili e si concentra su quelli difficili.
    
    **Libreria Python:** [focal_loss](https://github.com/umbertogriffo/focal-loss-keras)
    
    **Descrizione:** Implementazioni robuste della focal loss, inclusi BinaryFocalLoss e SparseCategoricalFocalLoss.
    
4. **Cost-sensitive Learning**
    
    Incorpora il costo degli errori di classificazione nel modello, penalizzando maggiormente gli errori sulle classi minoritarie.
    
5. **Ensemble Methods**
    
    Tecniche come bagging e boosting per combinare più modelli e gestire lo sbilanciamento.
    
    **Libreria Python:** [sklearn.ensemble](https://scikit-learn.org/stable/modules/ensemble.html)
    
    **Descrizione:** Implementazioni di vari metodi ensemble, incluso il boosting.
    

## 3. Data Splitting

La divisione del dataset per il fine-tuning consiste nel suddividerlo in set di training e validation, tipicamente seguendo un rapporto 80:20. Le principali tecniche includono:

1. **Random Sampling**
    
    Selezione casuale di un sottoinsieme dei dati per creare un campione rappresentativo.
    
    **Libreria Python:** [`sklearn.model_selection.train_test_split`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
    
2. **Stratified Sampling**
    
    Suddivisione del dataset in sottogruppi con campionamento da ciascuno per mantenere il bilanciamento delle classi.
    
    **Libreria Python:** [`sklearn.model_selection.StratifiedShuffleSplit`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedShuffleSplit.html)
    
3. **K-Fold Cross Validation**
    
    Suddivisione del dataset in K fold, con esecuzione del training e validation K volte.
    
    **Libreria Python:** [`sklearn.model_selection.KFold`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.KFold.html)
    
4. **Leave-One-Out Cross Validation**
    
    Utilizzo di un singolo punto dati come validation set e il resto per il training, ripetuto per ogni punto del dataset.
    
    **Libreria Python:** [`sklearn.model_selection.LeaveOneOut`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.LeaveOneOut.html)
    

## 4. Data Annotation

L'annotazione dei dati consiste nell'etichettare o taggare testi con attributi specifici rilevanti per gli obiettivi di addestramento del modello. È fondamentale per i compiti di apprendimento supervisionato e influisce significativamente sulle prestazioni del modello fine-tuned. Le principali modalità sono:

- **Human Annotation**
    
    Annotazione manuale effettuata da esperti umani, considerata lo standard per accuratezza e comprensione del contesto. Tuttavia, è costosa e richiede tempo su grandi dataset.
    
    Strumenti: _Excel_, [_Prodigy_](https://prodi.gy/), [_Innodata_](https://innodata.com/)
    
- **Semi-automatic Annotation**
    
    Combinazione di algoritmi ML con revisione umana per creare dataset etichettati in modo più efficiente, bilanciando accuratezza ed efficienza.
    
    Strumento: [_Snorkel_](https://snorkel.ai/) – usa weak supervision per generare etichette iniziali, poi raffinate da annotatori umani.
    
- **Automatic Annotation**
    
    Annotazione completamente automatizzata mediante algoritmi ML, senza intervento umano. Offre scalabilità e convenienza economica, sebbene l’accuratezza dipenda dalla complessità del task.
    
    Servizio: _Amazon SageMaker Ground Truth_
    

## 5. Data Augmentation

Le tecniche di Data Augmentation (DA) espandono artificialmente i dataset per affrontare la scarsità di dati e migliorare le prestazioni del modello. Tecniche comuni in NLP includono:

- **Word Embeddings** : Utilizzo di embeddings come Word2Vec e GloVe per sostituire parole con equivalenti semantici, generando nuove istanze testuali.
    
- **Back Translation** : Traduzione del testo in un'altra lingua e ritorno all'originale per ottenere parafrasi.
    
    Strumento comune: _Google Translate API_
    
- **Adversarial Attacks** : Generazione di dati aumentati tramite esempi avversari che modificano leggermente il testo per creare nuovi campioni mantenendo il significato.
    
    Libreria: _TextAttack_
    
- **NLP-AUG** : Una libreria che offre diversi augmenters per caratteri, parole, frasi, audio, spettrogrammi e altro, migliorando la diversità del dataset.
    

## 6. Synthetic Data Generation using LLMs

Large Language Models (LLMs) can generate synthetic data through innovative techniques such as:

- _Prompt Engineering_: Crafting specific prompts to guide LLMs like GPT-3 in generating relevant and high-quality synthetic data.
- _Multi-Step Generation_: Employing iterative generation processes where LLMs generate initial data that is refined through subsequent steps. This method can produce high-quality synthetic data for various tasks, including summarising and bias detection.

#dpKnowledge 