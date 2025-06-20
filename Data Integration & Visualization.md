#miscellaneous 
Data visualization:
La Data Visualization è l'arte e la scienza di rappresentare i dati in modo visivo per renderli comprensibili, comunicativi e facilmente interpretabili. Attraverso grafici, mappe e diagrammi, permette di identificare pattern, tendenze e insight nascosti nei dati.
Matplotlib (così come anche Seaborn, Plotly, Bokeh) sono strumenti fondamentali in questo ambito.
Buone pratiche nella Data Visualization
Mantieni la chiarezza: Evita grafici troppo complicati.
Scegli il grafico giusto: Ad esempio, usa un grafico a torta solo se vuoi mostrare proporzioni.
Aggiungi etichette e titoli: Rendi i grafici comprensibili.
Usa colori con criterio: Evita eccessi, scegli palette adatte.


Data Integration:
Processo di combinare dati provenienti da diverse fonti in un unico sistema unificato per facilitarne l'analisi e l'utilizzo e creare un'unica fonte di verità.
La più grande difficoltà può provenire dal fatto che spesso i dati provengono da fonti diverse (db, excel, api ecc..) e sono in formati diversi (json, xml, csv ecc..)

Tecniche di integrazione
1)ETL (Extract, Transform, Load):
Extract: Raccogli i dati dalle fonti.
Transform: Pulisci e riformatta i dati.
Load: Carica i dati in un data warehouse o database centralizzato.
Strumenti popolari: Apache NiFi, Talend, Informatica.

2)Stream Processing:
Utilizzato per gestire dati in tempo reale (es. log di sensori IoT).
Strumenti: Apache Kafka, Flink, Spark Streaming.

3)Data Federation:
Accesso a dati distribuiti senza doverli spostare fisicamente.
Strumenti: Denodo, Presto.