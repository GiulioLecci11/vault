Le regularization techniques sono strategie utilizzate in machine learning per migliorare la capacità di generalizzazione di un modello, ovvero la sua performance su dati non visti durante l'addestramento, essa in generale va a scongiurare l'overfitting
Overfitting: Quando un modello performa molto bene sui dati di addestramento ma fallisce su dati nuovi, perché "impara a memoria" invece di cogliere pattern generalizzabili.
Obiettivo: Bilanciare bias e varianza, migliorando la robustezza del modello.

Tecniche di Regularizzazione:
a. Penalizzazioni sui pesi: L1 e L2 Regularization
L1 Regularization (Lasso):

Penalizza la somma dei valori assoluti dei pesi.
Spinge i pesi di feature irrilevanti a zero, risultando in modelli più semplici (selezione automatica delle feature).
Adatto quando ci sono molte feature, ma solo alcune sono importanti.
Augmented loss= Loss+lambda* summation(|wi|)

L2 Regularization (Ridge):

Penalizza la somma dei quadrati dei pesi.
Spinge i pesi verso valori piccoli ma non zero, riducendo la complessità del modello senza eliminare feature.
 Utile in modelli con molte feature correlate
Augmented loss= Loss+lambda*summation(wi^2)
Dropout
Definizione: Tecnica per il deep learning che disattiva casualmente un sottoinsieme di neuroni durante l’addestramento.
Scopo: Evitare che il modello dipenda eccessivamente da specifici neuroni, promuovendo la diversità delle rappresentazioni.
Per esempio in una rete con 100 neuroni per layer, con 𝑝=0.5, circa 50 neuroni per layer saranno inattivi ad ogni batch.

Batch Normalization
Definizione: Normalizza gli input di ogni layer per ogni batch durante l’addestramento.
Scopo:
Riduce il problema dello shifting interno della covarianza (cambiamenti nei valori intermedi durante l’addestramento).
Permette tassi di apprendimento più alti e rende la convergenza più stabile.
Consiste nel calcolare per ogni batch media e deviazione standard e poi sottrarre ad ogni elemento della batch la media e infine dividerlo per la deviazione standard.
Questo procedimento viene utilizzato praticamente sempre.

Early Stopping
Definizione: Interrompe l’addestramento quando le prestazioni su un set di validazione smettono di migliorare.
Scopo: Prevenire l’overfitting fermando l’addestramento prima che il modello impari rumorosità dai dati.
Esempio:
Monitorare la loss di validazione e fermare l’addestramento se non migliora per troppe epoche consecutive o se si entra in overfitting (validation error smette di decrescere insieme al train error ed inizia a crescere)

Data Augmentation
Definizione: Modifica i dati di input per aumentarne la diversità senza raccogliere nuovi dati.
Scopo: Ridurre l’overfitting e rendere il modello più robusto.
Esempio:
Per immagini: rotazioni, zoom, aggiunta di rumore.
Per testo: parafrasi, sostituzione di sinonimi.

Quando usare quale tecnica?
Se noti overfitting: Prova L2 regularization o dropout.
Se l’addestramento è lento o instabile: Usa batch normalization.
Se hai molti dati: Early stopping e L1 regularization.
Se non hai molti dati: Usa data augmentation.
#AI 
[[Neural Networks]]
[[Cognitive Computing Summary]]
