Presentato nel [paper](https://arxiv.org/pdf/2505.12540), afferma che esiste uno spazio semantico UNIVERSALE che poi permette di arrivare a ciascuno dei possibili spazi semantici derivanti dall'addestramento di uno specifico modello su uno specifico dataset con degli specifici hyperparametri.

Il "traduttore", ovvero modello che mappa da spazio universale a singolo embedding space è detto **vec2vec**.

![[vec2vec.png]]
### Riassunto

Il paper introduce **vec2vec**, il primo metodo in grado di tradurre rappresentazioni testuali (embedding) da uno spazio vettoriale a un altro **senza dati accoppiati**, **senza encoder** e **senza set di corrispondenze predefinite**.

Questa tecnica consente di **mappare embedding generati da modelli diversi in uno spazio semantico comune**, preservando la geometria e il significato sottostante.

---

### Contesto e motivazione

- Gli embedding testuali sono fondamentali in molte applicazioni NLP, ma modelli diversi producono spazi vettoriali incompatibili.
- Il lavoro si basa sull'**Ipotesi della Rappresentazione Platonica**, che suggerisce l'esistenza di una struttura semantica latente universale condivisa tra modelli diversi.
- Viene proposta una versione più forte di questa ipotesi, denominata **Strong Platonic Representation Hypothesis**, che afferma che tale struttura latente può essere appresa e utilizzata per tradurre rappresentazioni tra spazi diversi senza dati accoppiati.

---

### Contributi principali

- **Traduzione non supervisionata**: vec2vec apprende una rappresentazione latente condivisa tra modelli diversi utilizzando solo embedding non accoppiati.
- **Preservazione della geometria**: le traduzioni mantengono alta similarità coseno tra embedding originali e tradotti (fino a 0.92).
- **Applicazioni di sicurezza**: dimostra che è possibile estrarre informazioni sensibili da embedding sconosciuti, evidenziando rischi per la privacy nei database vettoriali.

---

## Metodologia

- **Architettura**: utilizza perdite avversarie e consistenza ciclica per apprendere mappature tra spazi embedding.
- **Ispirazione**: si ispira a lavori su allineamento di embedding multilingue e traduzione di immagini non supervisionata.
- **Processo**:

1. Apprendimento di una rappresentazione latente condivisa.
    
2. Traduzione di embedding da uno spazio all'altro attraverso questa rappresentazione.
    

---

## Risultati

- **Efficacia**: raggiunge matching perfetto su oltre 8000 embedding mescolati senza accesso a corrispondenze predefinite.
- **Robustezza**: funziona su modelli con architetture e dataset di addestramento diversi.
- **Implicazioni**: dimostra che è possibile inferire attributi e ricostruire contenuti testuali da embedding sconosciuti.

---

### Implicazioni per la sicurezza

- **Rischi**: un avversario con accesso a embedding può potenzialmente ricostruire informazioni sensibili.
- **Necessità**: evidenzia l'importanza di proteggere i database di embedding e considerare la privacy nella progettazione di sistemi basati su embedding.

---

### Conclusioni

vec2vec rappresenta un avanzamento significativo nella comprensione e manipolazione degli spazi embedding, con importanti implicazioni per la sicurezza e la privacy. Il lavoro apre nuove direzioni per la ricerca su rappresentazioni semantiche universali e la protezione delle informazioni nei sistemi NLP.

#dpKnowledge 