La Principal Component Analysis (PCA) e la Linear Discriminant Analysis (LDA) sono due tecniche comuni di riduzione della dimensionalità utilizzate in machine learning e statistica.

PCA:
La PCA è una tecnica non supervisionata che riduce la dimensionalità dei dati trovando le direzioni principali (componenti principali) in cui i dati variano di più. Permette di:
Ridurre il numero di feature in un dataset mantenendo quante più informazioni possibili.
Trovare una rappresentazione più compatta dei dati.
Eliminare ridondanze e correlazioni tra le variabili originali.

In cosa consiste:
1)Si applica standardizzando i dati (come batch normalization) quindi (xi-med)/devstd

2)Viene calcolata la matrice di covarianza che misura come ogni feature è correlata alle altre, in particolare:
Gli autovalori rappresentano la quantità di varianza catturata da ogni componente principale.
Gli autovettori (detti componenti principali) rappresentano le direzioni nel nuovo spazio.

3) Infine i dati vengono proiettati su uno spazio di dimensioni ridotte usando le prime k componenti principali (quelle con i maggiori autovalori).

Così è possibile passare da, per esempio, 10 feature correlate tra loro a 3 feature che però racchiudono il 95% della varianza del dataset.
Tuttavia riesce a catturare solo relazioni LINEARI tra le feature.



Linear Discriminant Analysis:
La LDA è una tecnica supervisionata (si applica a feature e label) che riduce la dimensionalità massimizzando la separazione tra le classi. Permette di:
Ridurre dimensionalità preservando le informazioni più rilevanti per la separazione tra le classi.
Migliorare l’efficienza computazionale e ridurre l’overfitting nei modelli di classificazione.

In cosa consiste:
1) Si calcola la media per ogni feature
2) Si ottiene la matrice di dispersione interclasse e intraclasse
3) Attraverso gli autovalori delle matrici appena nominate si cerca di massimizzare il rapporto tra la varianza tra le classi e la varianza entro le classi
4)Infine I dati vengono proiettati su un nuovo spazio con dimensione pari K-1 con K il numero iniziale delle classi (feature).

Così si migliore la separazione tra le classi migliorando la classificazione, tuttavia non è adatto per dataset con forte overlap tra le feature
#miscellaneous 