Consiste in una versione avanzata di rag (utilizzata in Allianz) che invece di basarsi sul retrieve tramite similarità (bm25 o bm42 che sia) sfrutta l'elevata capacità di contesto degli ultimi modelli (gpt 4.1 nello specifico) per retrievare le informazioni. In particolare l'intera knowledge base (composta da dei file docx portati in markdown tramite markitdown, mahmoot e markdownify) viene "chunkata" e ad ogni chunk viene dato un id. Poi tutto viene messo in contesto e si chiede al modello di retrievare l'id del chunk con le informazioni necessarie a rispondere ad una determinata query.
In questo modo il time to first token è molto piccolo perché il modello non deve formulare una risposta ma retrievare solo l'id del chunk contenente le info necessarie e fornirlo in output. Questa soluzione abbatte le tempistiche di oltre il 70% rispetto ad altre soluzioni agentiche o gerarchiche.

Una volta che si ha l'id del chunk da retrievare per rispondere, un modello è incaricato di rispondere usando il contenuto del chunk (preso da un vocabolario con chiavi gli id dei chunk).

Questo tipo di soluzione è ottimale quando si ha a che fare con knowledge base molto dense ma piccole (Allianz ha "pochi" file, circa 15, ma tutti questi sono estremamente carichi di informazione e il rumore è quasi zero)
#AI 
[[Advanced RAG]] [[Agentic RAG]]
