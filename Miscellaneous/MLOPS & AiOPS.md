MLops:
Insieme di pratiche per gestire ed automatizzare il ciclo di vita di modelli di Machine Learning (come DevOps che però è più generico per prodotti SW in generale)

MLops è utile per:

Mettere in produzione i modelli di Machine Learning in modo più rapido e affidabile.
Automatizzare i passaggi necessari per costruire, addestrare, testare e aggiornare i modelli.
Monitorare e migliorare i modelli nel tempo: per esempio, quando i dati cambiano o le prestazioni del modello peggiorano.

Questa strategia viene utilizzata quando si ha a che fare con modelli ML che devono essere impiegati in un'applicazione reale e quindi si vogliono automatizzare fasi come la raccolta dei dati, il training ecc..

Esempio con un modello che prevede i prezzi delle case

Raccolta dati: Il sistema raccoglie continuamente dati sui prezzi delle case.
Addestramento del modello: Ogni mese, il modello viene riaddestrato con i nuovi dati.
Deployment: Il modello aggiornato viene automaticamente distribuito all'app.
Monitoraggio: Il sistema controlla se il modello sta facendo previsioni accurate. Se non lo è, invia un avviso.



AIops:
Utilizzo dell'intelligenza artificiale e ML per automatizzare e ottimizzare le operazioni IT (monitoraggio di infrastrutture, app, sistemi ecc..)

AIops è utile per:
Rilevare problemi IT (come un server sovraccarico o un'applicazione lenta) prima che diventino gravi.
Automatizzare la risoluzione dei problemi, riducendo il tempo di inattività.
Analizzare grandi quantità di dati IT (log, metriche, eventi) per trovare pattern o anomalie.

Esempio con un sito di e-commerce:
Monitoraggio: L'AI monitora il traffico del sito e i server.
Rilevamento: L'AI nota che il server principale è sovraccarico.
Azione automatica: L'AI sposta automaticamente parte del traffico su un altro server per evitare rallentamenti.

Alla fine quindi la principale differenza tra i due è il focus: in quanto AIops si concentra sull'utilizzare l'AI per operazioni IT, come appunto la gestione di un sito o un'app. Mentre MLops sull'automatizzare operazioni che riguardano direttamente l'AI come l'addestramento, la raccolta dati e il controllo delle performance.

#AI