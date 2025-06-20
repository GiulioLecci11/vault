#AI 
cap1: (AI Agent)
intelligent agent (actuators, sensors, policy)
autonomus agent
enviroment(observable,determ-stochas,discr-continuos,adversarial-benign,episodic-sequential)
PEAS (performance, env, act, sens)
1)simple reflex (action table, env must be fully observable)
2)model based (agent has a model of the world, thus it knows how the world will change based on its actions)
3)goal based (ha il modello e sceglie se fare o no un'azione in base al gol prestabilito)
4)utility based (reinf learning style with reward and punishment)


cap2:
cognitive architecture (agent+policy=codice)
core cognitive abilities:
1)perception (trasformare info in rappresentazione interna)
2)attention (selezionare solo parti importante e ridurre carico informativo a quelle parti e basta)
3)action selection
4)memory (procedural, semantic, episodic) [chunking: spezzettare conoscenze per crearne nuove])
5)learning
6)reasoning (ability to process knowledge to infer conclusions)
7)metacognition (ragionare sul ragionamento, come abbiamo scelto e perché)
8)prospection (anticipate the future basing on actions)
chunking (it means creating new rules (increase the knowledge) from the episodic memory)


cap3:
ML success (GPU,bigger storage for dataset)
superv,unsuperv,reinf
regression, classification


cap4:
Linear regression:Un modo per predire una quantità della quale non si conosce l'andamento rispetto a delle variabili. Si stima una funzione di variabile reale. In input abbiamo una serie di xi ciascuno col suoi yi e vale yi*=theta0*1+theta1*xi1+theta2*xi2.
La loss function che si usa è minimi quadrati cioè L=(yi-yi*)^2=ei^2=e'*e=(y-y*)'*(y-y*) ma y=x*theta e quindi theta=(X'*X)^-1* X' *Y
MAE per l'accuracy= 1/n * sommatoria |y-y^|


cap5:
Ockam Razor principle (modello migliore spesso modello più semplice).
Test e train set.
Per minimizzare loss non si può approccio analitico quindi si fa approccio iterativo.
underfitting (no buon train) overfitting (super buon train)-> validation set e si ferma quando valid error>train error


cap6: Principal Component Analysis
richiami di algebra su basi e spazi vett (spazi dove ogni elemento è vettore e sono tutti formati da comb lineari di elementi della base).
Applicazione lineare permette di trasformare un vettore da uno spazio vett in un altro spazio vett
Feature di dataset tanto importanti quando tanta varianza e meglio se poco correlate tra loro (poca covarianza) quindi si cambia sist di ref per evidenziare varianza di ogni variabile rispetto alle altre e diminuire covarianza. (trovare una base P che ci porti nello spazio Y dove la matrice di covarianza è diagonale, cioè ha numeri diversi da zero solo sulla diagonale)
si sottrae media a dati, si calcola matrice covarianza come 1/n-1 * X'*X poi si prendono autovettori che formano la base per cambiare sist (La matrice di covarianza definisce (come abbiamo visto prima si può definire un'app lineare con una matrice) un'applicazione lineare che trasforma il cerchio unitario in un'ellisse che "racchiude" la distribuzione dei dati. Gli autovettori identificano gli assi mentre gli autovalori la loro lunghezza)
Dopo la trasformazione, il dataset ha feature scorrelate tra loro (lin indip) che sono allineate sui nuovi assi e hanno, dalla prima in poi, sempre meno varianza (Le componenti più importanti sono identificate dagli autovalori più alti)


cap7: classification (no NLP in questa materia) e Support Vector Machine (SVM)
Questa è in soldoni un puro problema geometrico risolvibile trovando uno o più classifiers (linee o piani) nello spazio
classification si fa in base a segno dell'equazione di una retta che è wx+b (tante w e x quante sono le dimensioni dell'iperpiano), se più classi allora classe scelta è argmax wix+bi trovando la i per cui w e b danno l'equazione che con quella x da il numero maggiore, i va da 0 al numero di classi
Poi si usa in realtà softmax per passare a densità di prob dove
P(yi|x)= e^(wix+bi)/sommatoria( e^(wjx+bj)).
La prob totale di classificare tutto correttamente, essendo probabilità statisticamente indipendenti, è data dal prodotto delle varia probabilità
Quindi possiamo prendere come loss la "negative log likelyhood" detta anche cross entropy loss, che consiste nel passare al logaritmo di P(y|x) e per le proprietà dei logaritmi si ha la somma invece del prodotto. Poi siccome vogliamo minimizzare, si mette il segno meno davanti al log che è come massimizzare la probabilità di successo. La loss va da 0 a +inf.

Come scegliere un classifier (una retta o un piano) rispetto ad un altro?
Support Vector Machine: metodo per trovare il classifier (retta se 2D) più adatto basandosi sulla distanza dal support vector (elemento del dataset più vicino alla retta). Esso consiste nell'esprimere W parametreo della retta di separazione come w= SOMMATORIA DI c*y*x dove c = 0 se elemento NON è support vector, y e x vengono dal training set.
Può succedere che classifier lineare non vada bene (non una retta ma servirebbe una curva) allora si possono attuare delle trasformazioni non lineari sulle features come ad esempio x^2->x e y^2->y così da poter attuare linear classification. Si usa quindi un KERNEL TRICK per andare a sostituire x con una funzione che ne calcola direttamente la trasformazione non lineare in maniera da rendere meno complesso il passaggio.
SVM per multiclassi si possono realizzare come 1vs1 (1v2,1v3,2v3) oppure a gruppi (1v2-3, 2v1-3, 3v1-2)

1vs1:
supponendo di avere 3 classi (1-2-3) creo 3 SVM, uno per ogni coppia di classi
1-2
1-3
2-3
se ricevo in input un elemento della classe 1, gli output degli SVM saranno
1-2  -> 1         perché SVM capace di riconoscere gli 1
1-3  -> 1         perché SVM capace di riconoscere gli 1
2-3  -> 2         perché SVM incapace di riconoscere gli 1, riconosce solo 2 o 3
quindi confrontando gli output capisco che l'input appartiene alla classe 1

1vs all: SVM del tipo 1-(23)   2-(13)  3-(12), qui la predizione di una classe 1 è più probabile che venga dal primo SVM. Per esempio il primo darà 1, il secondo darà 13 e il terzo darà 12 quindi è ovvio che sia 1


cap8: DECISION TREES anche con linear regr
decision trees (explainable intrinsic), pongono domande diagnostiche. Devo partire dalla domanda più determinante (dividi e conquista) e più informative.
Nota che the more features (attributes) we have in our dataset, the more we risk to overfit
Class purity misurata da 
1) entropia= -prob i * log(prob i)-...
2) information gain: IG(parent,child)=entropia parent - proporzione child * entropia child - ... con proporzione child si intende NUMERO DI ELEMENTI CHILD INDIPENDENTEMENTE DALLA CLASSE (12 di una classe, 3 di un'altra su 40 elementi, proporzione è 15/40)
dove child è il risultato del dataset a seguito di una domanda (balance <50K per esempio)
In caso di regression non c'è una classe da discriminare quindi invece di class purity si studia la varianza del valore che vogliamo predire
overfitting se troppo specifico, pruning in caso per rimuovere foglie inutili. 
Se un nodo inutile si vede dal significato statistico ottenibile con test chi^2 che da delle gaussiane che NON devono sovrapporsi


cap9: K nearest neighbor
knn è un metodo di predizione supervisionato (classificazione o regressione) basato sulla simililarità tra i dati. L'output per un campione in ingresso (di classe non nota) viene stimato in base ai vicini (di classe nota) in genere tramite un criterio di maggioranza.
Si possono usare diverse distanze:
1)euclidea: differenza feature al quadrato sotto radice
2)minkowski: norma euclidea^p
3)manhattan: semplice valore assoluto, norma L1
Questo algoritmo non è ottimo quando lo spazio delle feature ha troppe dimensioni poiché in tante dimensioni i punti non sono mai realmente vicini.
Buono per anomaly detection e outliers.
NOTA CHE NON SI ALLENA NESSUN MODELLO, QUESTO E' INFATTI UN <<MEMORY BASED REASONING>> (O NON PARAMETRIC MODEL O ANCORA INSTANCE BASED LEARNING)

NON SI ALLENA nessun modello, è un memory based reasoning (non parametric model)
si è soliti standardizzare i dati per evitare che si coprano tra loro (meno media diviso std dev)



cap10: SPIEGAZIONE BACKPROP
neural networks basate su neuroni del cervello ma SENZA la nozione di tempo
al neurone arrivano vari ingressi e lui computa
sommatoria di wi*xi+b alla quale poi applica funzione di attivazione.
1)sign: non lineare, utile per classifier
2)sigmoide: da 0 a 1, poi satura, come se fosse probabilità, vale 0.5 in 0
3)tanh: come sigmoide ma varia tra -1 e 1, vale 0 in 0
2 e 3 hanno derivata nulla se lontani da 0 perché diventano costanti
perceptron: tanti neuroni connessi ad uno che prende ingressi e applica funz di attivaz. Ha un output per ogni classe se deve classificare.
Multilayer preceptron (MLP) se tanti livelli hidden (max 3 sennò overfit); modello feedforward aciclico (ogni layer dipende SOLO dal layer precedente, rete feedforward). livelli hidden hanno activ funct non lineare e livello di output NON HA activ funct.
Matricialmente w'*x+b (argmax se multiclass classifier)

Si allena con BACKPROPAGATION andando a minimizzare loss iterativamente, partendo da un punto casuale dello spazio (impossibile esplorarlo tutto) e spostandosi nella direzione opposta al gradiente alla ricerca di un minimo locale (il gradiente è un vettore nello spazio dei parametri che punta alla direzione verso cui la funzione aumenta (loss function in questo caso). Esso è composto da ogni derivata parziale della funzione). Theta(t+1)=Theta(t)+delta theta dove delta theta dipende dalla DERIVATA DELLA LOSS FUNCTION RISPETTO AI PARAMETRI DELLA RETE e il modo in cui sono legati corrisponde a una delle formulazioni del gradient descent. Il vero motivo per cui è difficile calcolare questa cosa è che there's no "correct output to compare with" for hidden layers, it only exists for output layer
affinché ciò si possa attuare devono valere 4 condizioni:
1) la loss deve essere una funzione composita ossia L(zn(zn-1...)))
2) deve essere calcolabile la derivata della loss rispetto all'output della rete
3) deve essere calcolabile la derivata dell'output di un livello rispetto al suo input, per ogni livello
4) deve essere calcolabile la derivata dell'output di un livello rispetto ai suoi parametri, per ogni livello

Attenzione che i gradienti devono sempre rimanere informativi e mai andare a zero (altrimenti come sposti nella direzione opposta?), quindi si usa la ReLU (max tra 0 e x) che non diventa mai costante lontana da 0, anche se ciò comporta dei neuroni che danno sempre 0 (dead ReLU neurons)

Una volta ottenuta dL/dTheta si può ottenere delta theta in 3 modi. QUESTI SI IMPOSTANO NELL'OPTIMIZER
1)Batch gradient descent: media su INTERA EPOCA di dL/dTheta e aggiornamento del gradiente ogni epoca (lentissimo)
2)Stochastic gradient descent: calcolo di dL/dTheta e aggiornamento del gradiente ad ogni ingresso (instabile, veloce)
3)Mini Batch gradient descent: media su INTERO BATCH di dL/dTheta e aggiornamento del gradiente ogni batch (equilibrato)

Tutti questi metodi hanno a moltiplicare il learning rate "ni" che viene aggiornato autonomamente da optimizer ADAM



cap11:
clustering (UNSUPERVISED learning) basato sul raggruppare dati sulla nozione di similarità
2 tipi di clustering, in generale non diciamo noi all'algoritmo come raggruppare i dati ma lo fa lui rivelando anche pattern nascosti
1)hierarchical clustering: si parte da ogni nodo preso singolarmente che è un cluster e poi questi cluster vengono fusi tra loro finché non ne rimane uno solo

2)k-means: metodo di clustering non supervisionato, procedura iterativa basata su centroidi (k centroidi) che vengono prima messi randomicamente e poi ricalcolati iterativamente fino al raggiungimento dell'equilibrio.
you can find some clusters that make sense and others that are just noise, find a meaning stands to us but maybe we have to use the right k to find a meaning
se aumenti k->fai più gruppi->vengono meno popolati
se diminuisci k->fai meno gruppi->più popolosi


cap12:	CROSS VALIDATION
La cross-validation è usata per effettuare la selezione degli iperparametri (num layer, num neurons, LEARN RATE) ottimali per un modello. Per ogni possibile configurazione di iperparametri, si effettua la cross-validation; alla fine, si sceglie la configurazione che ha fornito i risultati migliori. È importante capire che in questo caso non viene stimata la capacità di generalizzazione del modello: ovvero, i fold di validation non possono essere considerati come un test set, perché vengono attivamente utilizzati nella procedura di training. 
Per valutare la generalizzazione, è quindi necessario un set di test esterno al dataset usato per fare cross-validation.

Oltre ciò la cross validation è usata per evitare che la "fortuna" influisca sulle performance del modello, allenandone diversi su diverse fold del training per ottenere performance medie (ecco perché la stima delle performance è più affidabile rispetto ad una stima singola) e CONFIDENCE INTERVAL (che devono essere non sovrapposti per provare superiorità netta di performance, altrimenti ci sono delle possibilità che un metodo performi meglio alcune volte e peggio altre).

La nested cross-validation fornisce invece un approccio che integra sia la selezione degli iperparametri che la valutazione della
generalizzabilità del modello. 
In pratica, viene impostata come una "normale" cross-validation, dividendo il dataset in k fold, e lasciandone uno da parte di volta in volta: questo fold verrà usato come "test set", e sarà usato per valutare la capacità del modello di generalizzare. Il "training set" rimanente (cioè, l'unione di tutti i fold tranne quello di test) non viene usato direttamente per allenare, ma viene utilizzato anch'esso in cross-validation, dividendolo in "sotto-fold" al fine di stimare i migliori iperparametri: una volta trovati, questi verranno poi testati sul fold di test estratto all'inizio. In genere si fa riferimento alla prima cross-validation (per la stima della generalizzazione) come "outer loop" e alla seconda (per la selezione degli iperparametri) come "inner loop".


cap13:
spatial resolution: quanto spazio reale copre un pixel in un'immagine (meno spazio copre, più pixel ci sono, più alta risoluzione)
radiometric resolution: quante gradazioni di colore sono identificabili per ogni pixel (256 se 8 bit per ogni pixel). 
Tramite l'uso di filtri possiamo trasformare un pixel in un altro, la trasformazione può dipendere anche dai pixel circostanti il pixel in questione. I linear filters sono chiamati <convolution> in quanto "Output pixel 𝑔(𝑥,𝑦) is computed as the linear combination between filter elements and the corresponding elements in 𝑓 that overlap the filter centered at (𝑥, 𝑦)"
Esempio:
we overlap the kernel (convolution mask) to the image and the computation is performed on the source pixel, with the surrounding ones.
In breve si fanno tutti i prodotti elemento per elemento tra pixel dell'immagine e quadratini del filtro (0x4, 0x0, 0x0, 0x0, 1x0, 1x0, 0x0, 1x0, 2x-4) e si sommano tutti i risultati nel pixel centrale corrispondente detto destination pixel.

Problematica dei borders: perché non puoi centrare la mask sui pixel del bordo, qualche pixel della maschera uscirebbe dal bordo, quindi o non si processano i pixel nei bordi o si aggiungono dei pixel finti (aka padding) con varie tecniche (copying, mirroring ecc..) per poter applicare le maschere correttamente
smoothing filters ogni pixel è sostituito dalla media aritmetica dei filtri che lo circondao, la maschera consiste quindi in 1/k^2. L'output risulta l'immagine originale ma blurrata, filtri derivativi (righe o colonne di -1-2-1 in base a se si vuole fare edge detections orizzontale o verticale)
se immagine ha n canali, kernel ha n canali ma output image ha SEMPRE UN SOLO CANALE

Una layer convoluzionale, a differenza di un MLP, non ha tutti i neuroni connessi tra loro e i pesi sono condivisi per posizioni (primo collegamento ha sempre stesso peso, secondo pure ecc.. Quindi parameters sharing (molti meno parametri, molto meno overfit)) perché le connessioni sono realizzate con lo shift della kernel mask sui vari neuroni del livello precedente. Inoltre essi accettano anche matrici in input e non perdono la cognizione di spazialità (invece un multilayer perceptron andava allenato con le immagini trasformate in un vettore e quindi si perde tutta l'informazione di "surround" nel senso che se prendo un cubo 3x3 di pixel e lo trasformo in un vettore 1x9 allora non saprò più quale pixel stava sopra (o sotto)
un altro pixel ecc, e magari questa info era importante)
Ogni kernel (convolution mask) detecta feature differenti di un'immagine
parametri conv layer: 
1)kernel size (grandi -> più parametri -> più influenza da pixel vicini)
2)stride (spazio tra pixel immagine originaria tra un prodotto e un altro), stride has the effect of reducing the output image
3)padding
4)dilation (spazio tra elementi kernel)
transposed convolution aumenta dimensioni immagine invece di diminuire.

cap14: cnn, regularization, pooling, dropout e batch normalization
nelle CNN ogni layer (tranne l'ultimo che di solito è un classifier) riconosce feature sempre più complesse (difatti aumenta numero di canali che è numero di features e diminuisce numero di pixel).
M feature maps corrispondono ad M convolutional kernels, i cui valori delle matrici vengono però appresi durante il training 
Le feature vengono apprese in automatico dalla rete, no feature engineering (però no explainable).
Per ogni livello dopo la convoluzione si applica una ReLU. Poi si applica POOLING (max pooling ritorna solo valore più grande all'interno di una window) per ridurre dimensioni delle mappe MA NON IN OGNI LIVELLO.

CNN combinate a traditional neural networks per classification ma bisogna passare input come singolo vettore (view).
so at the end we have small feature map that represent single concepts (ears, eyes ecc). We don't have spatial information anymore.
nell'immagine iniziale avevamo tutte informazioni spaziali (importante quale pixel sta sopra, quale sotto ecc) nella feature map finale abbiamo solo dei concetti (ha ruote? no, occhi? si, gambe? si ecc..). Questo vettore finale verrà dato in pasto al MLP o comunque ad una rete di fully connected layers e da lì usciranno le probability di appartenenza a ciascuna classe.

CNN tendono ad overfittare quindi si usa regularization cioè Loss=E(y,y*)+lambaR con lambda che è WEIGHT DECAY e R=1/2 * normaL2 di theta^2 (facile da derivare e mantiene bassi i valori di theta in quanto si mettono nella funzione da minimizzare). Così si riduce capacità di apprendimento e rischio di overfit perché si danno più compiti da risolvere contemporan.

Dropout per evitare neuron coadaptation (due neuroni dello stesso layer imparano a riconoscere ciascuno parti diverse della stessa feature e un neurone del layer successivo impara a riconoscerne le feature totale)

Batch normalization= standardization di ogni batch in uscita da ogni layer e genera data augmentation perché se batch diverse, medie e std dev diverse.
A batch normalization layer standardizes a layer’s output based on mini-batch statistics
Data augmentation data anche da random crop random flip ecc.
Batch norm e standardization degli input vengono applicati sia in train che in test phase. Regularization tramite weight decay solo nel train, così come dropout.

Training from scratch o fine tuning (CNN cioè feature extractor frozen, classifier da allenare)


cap15: GAN + Semantic Segmentation
discriminative models imparano P(y|x) mentre generative models imparano P(x,y)
GAN (gen adv netw) hanno discriminator e generator che si allenano insieme per fregarsi a vicenda.
Discriminator allenato con vere immagini (label vero) e immagini generate (label falso) mentre generatore prende in input un rumore e lavora di transposed conv per generare immagini. Questo si allena dando suo output a discriminatore e dicendo a discriminatore che label è vera, i pesi del discriminatore NON si aggiornano ma la backprop viene backpropagata al generatore per capire dove sbaglia.
NON alleniamo generatore con immagini già esistenti sennò le duplica e basta.
Rumori simili generano immagini simili. Si usa Inception score per capire quanto buono il sistema. Sistema buono se genera tante immagini di classi diverse ma tutte molto riconoscibili. 
Problemi se uno dei due diventa troppo bravo sull'altro o se generator impara a fregare l'altro con immagini no sense.

Conditional GAN se generiamo immagini con una label (solo immagini di quel tipo) e indoviniamo sia immagini che label.

Representation learning quando non si ha un vero task di classificazione ma si vuole imparare a rappresentare l'input in maniera più compatta possibile. è UNSUPERVISED.

AutoEncoder sono encoder e decoder che fanno ste cose uno conv e l'altro transposed conv.

Semantic segmentation: pixel level classification (trovare oggetti nelle foto ecc..)
realizzata con autoencoder oppure con UNET architecture fatte da CNN (descending, compress the info) poi fully connected layers (bottleneck, minima rappresentazione dell'input) e poi transposed conv(ascending, expands the features, its output number of feature maps is the number of different classes) collegate tramite skip connections per passare informazioni che non vogliamo si perdano nella convoluzione (features ad alta risoluzione).
Spesso hanno problemi di class imbalance che si risolvono con pesi diversi per le classi nella loss



cap16:
nel Model assestment si vogliono analizzare le performance da un punto di vista dell'utente e non del modello. Si deve quindi fare distinzione tra i vari tipi di errori che si commettono (invece in termini di accuracy importa solo che si è commesso l'errore e non come) quindi si distinguono TP,TN,FP e FN che popolano le CONFUSION MATRIX.
Inoltre esistono delle metriche come precision (TP/TP+FP) e sensitivity o RECALL (TP/TP+FN) per misurare come gli errori sono distribuiti. C'è poi F1 score (2*(Precision*Recall)/(Precision+Recall))
Quello visto finora valeva per una classe (e "tutto il resto") per quando si interagisce con molte classi si possono mediare quelle 3 metriche (Precision, sensitivity e F1-score) oppure mediarle con pesi diversi in base alla classe o ancora media TP,TN,FP ed FN e poi calcolare le metriche.

Per contestualizzare l'utilità del modello c'è poi l'expected value che comprende ragionare in termini di struttura del problema e cosa possiamo estrarre dai dati.
Exp value=Prob(evento1)*Valore(evento1)+.. dove il valore può essere un costo o un beneficio in base all'evento. Se l'expected value è negativo, perderò sicuramente soldi quindi deve essere ALMENO positivo.
Combinando la confusion matrix (normalizzata tramite i rapporti di true positive ecc) con la matrice dei costi e benefici provenienti dall'analisi economica (in particolare le moltiplichiamo e poi sommiamo) otteniamo l'expected value. Questo possiamo confrontarlo con agente stupido che predice sempre la classe più frequente oppure con decision stump cioè che sceglie randomicamente



cap17:
una strategia alternativa low budget è di non analizzare ogni singolo dato ma andare a costruire una graduatoria (ranking vs classifying) con gli output del classifier e studiare solo una percentuale di quelli più alti in classifica (plot profitto/percentuale su profit curve). In alcune situazioni il classifier potrebbe essere più conservativo (alte certezze necessarie prima di classificare come positivo) o in altre meno e quindi usare threshold più o meno alte sull'output score (classificati)
ROC space asse x false positive, asse y true positive.
AUC area under curve



cap18:
Si dice che rappresentare bene un problema sia la chiave per risolverlo. Una rappresentazione è un insieme di regole e convenzioni su come si descrive qualcosa, una descrizione invece è l'utilizzo di quelle regole per descrivere qualcosa.
Delle buone rappresentazioni permettono subito di capire il problema in maniera semplice (occhiata) esprimendo i suoi vincoli e le relazioni tra gli oggetti senza riportare nozioni irrilevanti.
Una SEMANTIC NET è una rappresentazione di concetti fatta di nodi e collegamenti tra nodi (modelli ER, grafi). Distinguiamo 3 scuole di pensiero semantiche
1)Equivalence semantics: Il significato di nodi e collegamenti è dato dai nomi
2)Procedural semantics: Il significato è dato dal programma e dal suo svolgimento
3)Descriptive semantics: Il significato è dato da delle descrizioni riportate.

Per esempio per identificare un oggetto lo si può descrivere e poi si compara la sua descrizione con una libreria di oggetti, vedendo se è presente (lui o una descrizione molto simile)
I problemi di ANALOGIA si verificano quando si vede come A è diventato B e si deve descrivere quale trasformazione è la più simile tra quelle che avvengono su C (regole descrittive sopra sotto fuori dentro ecc)



cap19: RNN & LSTM
recurrent neural networks gestiscono TIME STEP facendo si che ogni livello prenda in input anche il suo stesso output ma all'istante precedente. Adatta a contenuti sequenziali.
H(t)=tanh(W'*x+V'*h(t-1)+b).
La backprop è detta TROUGH TIME e fa si che appaia sempre la derivata rispetto ai pesi del time step precedente.
Ciò causa problemi nelle relazioni molto dilungate nel tempo, dove appare più volte la derivata della tangente iperbolica (che è minore di 1) causando i VANISHING GRADIENTS. 
Gli update dei pesi relativi agli input iniziali diventano troppo piccoli quindi input iniziali non agiscono su output finali

Se si usasse la ReLU si incorrebbe negli EXPLODING GRADIENTS per lo stesso motivo. Solitamente RNN hanno tantissimi layer.

Ecco perché nascono le long short term memory (LSTM) dotate di MEMORY CELL in ogni recurrent layer. Esse sono in grado di mantenere il valore di un neurone per tanti step e quindi il suo gradiente è backpropagato senza problemi. Queste funzionano coi GATE e CELL STATE:

1)Cell state (internal memory vector)= C(t-1)*Ft+Nt

2)Forget vector=sigmoide(W*x+V*f+b) dal FORGET GATE

3)Proposed cell state C^=tanh(W*x+V*f+b) essendo un cell, ha tangente iperbolica

4)It=sigmoide(W*x+V*f+b) dal INPUT GATE

5)nt(visto prima)=C^*It

per quanto riguarda l'output invece, ne abbiamo uno per il livello (NON è la cell state, ma si calcola usando quest'ultima) e uno per la cell

6)Ot=sigmoide(W*x+V*f+b) dall'OUTPUT GATE

7)cell output ht=tanh(Ct)*Ot

Nota come LSTM è fortemente non lineare usando internamente sempre sigmoidi e tanh (come sigmoide ma cambia valore in 0)

Esistono poi le GRU (gated recurrent unit) dove si usa un gate in meno in quanto inputgate=1-forgetgate. Cioè si aggiorna ciò che si dimentica e ciò permette di risparmiare sul numero dei parametri.

Infine ricorda distinzione delle RNN in base a quando ricevono input e quando forniscono output ManyToOne(video classification), OneToMany(Image captioning), ManToMany(FrameByFrame video class) e Traslated Architecture (many to many ritardata)


cap20:
Il problem solving by search è un procedimento che si applica quando ci si trova a dover scegliere tra diverse strade (CI MUOVIAMO ALL'INTERNO DI UNO SPAZIO DI STATI) per arrivare ad una soluzione e non si sa quale sia quella ottimale. La complessità è data dal fatto che ci sono tanti stati possibili.
Come il problema del navigatore, esistono diversi approcci:
1)Breadth first (tiene conto solo numero di step, se uguale va a caso)
2)Uniform cost search (tiene conto costo di ogni step, se uguale va a caso)
3)Bidirectional search (partiamo sia da inizio che da fine e vediamo quando due strade si incontrano)
Se poi si hanno delle conoscenze pregresse si può esplorare prima le direzione verso cui si pensa vi sia la meta e in quel caso si può essere più o meno greedy (ciò potrebbe portare a trascurare strade vincenti).
A* è un algoritmo simile a Uniform cost che però va a sommare anche le stime che si hanno sulle varie distanze(costo per raggiungere nodo + costo stimato da nodo a destinazione). ATTENZIONE che se le stime non sono corrette, il problema potrebbe essere non risolto (non completo o non ottimale). Risulta molto pesante e funziona bene se spazi piccoli.


cap21:
I transformers risolvono il problema del bottleneck delle RNN che NON SONO PARALLELIZZABILI e inoltre perdono connessione col passato dopo tanti timesteps (vanishing gradients) 
Encoder e decoder sono stackabili all'infinito in quanto si ha sempre la stessa dimensione dei dati in ingresso e in uscita.

Architettura encoder:
-Tokenizer (frase into pezzi di parole into numeri (token-ids) tramite dizionario). The most used approach is to divide sentences into piece of words in this way we are recycling tokens and we're convey semantics. This means that faster and fastest  are correlated since they use almost the same tokens.
-Embedding (mappa ogni token in uno spazio di dimensione #depthmodel (detto LATENT SPACE), ogni token del dizionario ad inizio allenamento è in un punto casuale ma a fine allenamento parole con significato simile sono vicine)
-Positional embedding (assoluto, normalizzato per lunghezza frase, sin(pari) e cos(dispari) a freq diverse per avere valori simili per parole vicine, oppure tramite learnable parameters)

Poi si entra nei sublayers (questi sono stackabili all'infinito) che via via processano queste rappresentazioni (provenienti dall'embedding) rendendole più complesse per noi ma intelligibili per il modello stesso

-Multiheaded self attention: basato sul principio che il significato di una parola può essere considerato come la somma delle parole a cui essa da più attenzione (la frase va in input a 3 linear layer diversi da cui escono query key e values (immaginali rispettivamente come una ricerca su yt, il titolo del video indicizzato con delle certe chiavi e il video stesso, in particolare each word in our sentence is a query and we compute the similarity respect other words (keys)) , i primi due si moltiplicano per ottenere la attention matrix (formula data dalla <<cosine similarity>> così che la matrice riporti dei valori più alti in corrispondenza di parole che si attenzionano tra loro) che viene poi divisa per dkey(solitamente=radice di dmodel, e viene fatto per stabilizzare i conti) e infine vi si applica una softmax per trasformare i valori di attenzione in probabilità tra 0 e 1, poi si moltiplica per i values ottenendo la context matrix definitiva e si concatenano i risultati di più teste (letteralmente cucendo assieme le context matrix provenienti dalle teste diverse), infine si riducono le dimensioni tramite linear layer ritornando alle dimensioni originali).
-linear layers per normalizzare e creare rappresentazioni diverse
Alla fine si aggiunge un classico feedforward layer che prende in ingresso direttamente l'output del multiheaded attention layer



Architettura decoder:
-Output embedding esattamente come input ma SOLO IN FASE DI TRAIN (teaching forcing) dove passiamo la frase che è la risposta ma con i token start e end of sequence. In fase di INFERENZA (TESTING) ciò non viene aggiunto

- 2 meccanismi per attention:
1)Masked self attention: identico a quello dell'encoder ma sommiamo matrice triangolare superiore fatta di -inf per non barare (non "vedere" ad ogni timestep tutti i token corrispondenti ai timestep successivi)
2)Cross attention IDENTICA a quella dell'encoder ma query viene da DECODER mentre keys e values vengono da ENCODER. NOTA CHE QUESTA CROSS ATTENTION NON ESISTE SE SI USA SOLO DECODER (GPT fino a 3.5)

-Output corrisponde a distribuzione di probabilità tra tutti i token del vocabolario




cap22:
Tramite la logica si riesce a ragionare sulle info che si hanno e fare inferenze per ottenere nuove informazioni. Un predicato è un verbo che indica una proprietà (che può essere vera o falsa) di un argomento. Possono essere presenti anche congiunzioni disgiunzioni, implicazioni e negazioni. Le cui tabelle di verità facilitano la creazione di inferenze. Una regola si scrive come antecedente:conseguenza.
MODUS PONENS (ali->uccello.   Se so che ha ali, so che è uccello. Conosco antecedente e ottengo conseguenza)

MODUS TOLLENS(ali->uccello.   Se so che no uccello, so che no ali perché se avesse ali sarebbe uccello. So che non vale conseguenza quindi no antecedente)
Universal quantifiers: Per ogni ed Esiste.
3 tipi di reasonings
1)Deduttivo: permette di ottenere conclusioni date le premesse (spesso ci facciamo influenzare da conoscenze del mondo)
2)Induttivo: basato sui casi già visti (esperienza passata), qualcosa è vero finché non si prova il contrario
3)Abduttivo: risalire alle cause partendo dagli effetti, tricky



cap23:
Explainable AI utile a capire come funzionano i modelli di ML, e perché prendono le decisioni che prendono. Ciò è difficile con modelli CNN perché fanno cose strane e non capibili, facile con decision trees perché intrinsecamente spiegabili ma performance inferiori.
Esistono diversi modi di spiegare un modello AI, che si dividono in
-Post hoc (dopo train) o intrinseci
-Local o Global
-Model specific o Model agnostic(works on data)

Tra quelli visti abbiamo (SONO TUTTI METODI LOCAL QUINDI SPIEGANO SOLO QUELLA PREDICTION)
quelli che lavorano solo con modelli differenziabili:
1)Integrated gradient (gradient normale fallisce sensitivity test, IG no) dando input via via sempre più significativi guardando evoluzione gradienti
2)GRADCAM (evoluzione del CAM che richiedeva di scambiare il livello finale di una CNN con un GAP (glob aver pooling)) che usa i gradienti per evidenziare l'importanza delle feature e ottenere delle heatmap da applicare alle immagini (si applica anche RELU per scartare contributi negativi).

e quelli che sono model agnostic
1)Shapley values (gioco a premi tra gruppi di features per vedere quanta importanza hanno)



cap24:
una versione del web che sia MACHINE UNDERSTANDABLE e non solo readable, migliorando l'indicizzazione delle pagine da parte dei motori di ricerca e permettendo di rispondere a richieste più complesse (ricerche basabili su significati e relazioni, non solo parole chiavi).
I dati semantici specificano un contesto semantico tramite relazioni. I dati connessi tra loro tramite relazioni creano delle ontologie (concettualizzazioni condivise di un certo dominio cioè insiemi di concetti collegati tramite relazioni). Web passato da statico a dinamico (interattivo) a semantico.

RDF: (resource description framework) vocabolario espressivo per creare relazioni. Introduce triple soggetto predicato oggetto.
Poi RDFS (schema) aggiungerà sottoclassi, domain e range.

la reificazione consiste nell'associare più info ad un blank node e poi quel blank node attribuirlo ad un altro nodo

OWL: (Web Ontology Language) linguaggio per fare inferenze e abilitare ragionamento automatico, introduce Esiste, Per ogni, And e Or e ha "problema" di ASSUNZIONI DEL MONDO APERTO (cioè tutto è possibile che sia vero finché non si dichiara che è falso). Permette di esprimere anche dei vincoli e vari modi in cui le proprietà possono essere (transitive, reciproche, simmetriche, inverse..)

Reasoner: motori inferenziali che controllano consistenza e correttezza dell'ontologia permettendosi anche di effettuare inferenze.

SPARQL per fare query con triple, ha SELECT, WHERE, ORDER BY, LIMIT ecc..

Linked Open Data sono dati condivisi annotati secondo gli standard (RDFS,OWL) come DBpedia



Lab:
optimizer serve a regolare learning rate (ADAM), e attuare back prop (infatti è qui che si imposta stochastic grad desc o mini batch ecc..).

Aumentiamo velocità con CUDA cioè GPU invece di CPU, ricorda di fare todev

Zero gradient azzera il gradiente ad ogni passaggio per evitare che si contaminino, cioè che il gradiente venga sovrascritto su quello precedente.

dataloader permette di caricare i dati dai vari set e randomizzarne l'ordine (ad ogni epoca mescoliamo il trainset così da non far abituare il modello con shuffle). Gli passiamo il dataset, la dimensione delle batch, il numero di thread workers e l'eventuale shuffle (solo nel train). 
drop_last: when the number of samples is not divisible by the batch size, the last batch will have a number of elements smaller than batch_size

datatransformation sono usate per raggruppare trasformazioni come randomcrop, toTensor, normalize ecc.. e sono diverse perché per esempio nel train non si fa data augmentation

livelli convoluzionali sono in ordine crescente di neuroni (1,64 poi 64,128 ecc) infatti aumentano via via quantità di feature maps e riducono risoluzione. Inoltre maxpool non si mette sempre. Mentre livelli feedforward sono lineari e vanno a diminuire il numero di neuroni fino a raggiungere il numero di classi 

problemi e vantaggi di kernel convoluzionali troppo grandi: kernel + grande-> + info contemporaneamente e riduzione + rapida della grandezza delle immagini (invece di dover fare più layer) però se feature troppo piccole non saremmo in grado di riconoscerle e può causare overfit e inoltre aggiunge tanti pesi alla rete, complicandola

batch normalization (levare media e dividere per std dev) si CALCOLA (computa) solo su train set ma si APPLICA anche a test set (anche perché considera che come test set si potrebbe anche passare un sample solo quindi non si potrebbero calcolare quelle metriche)

est_x = train_dataset[0][0].unsqueeze(0) #unsqueeze aggiunge una dimensione
#aggiungiamo una dimensione perché dobbiamo dare in input alla rete sempre un batch e quindi come prima dimensione dobbiamo avere la batch dimension
#quindi aggiungiamo una dimensione a mano e la mettiamo uguale a 1 così da dire che abbiamo i batch che contengono 1 elemento, quando poi
#useremo dataloader ci penserà lui a mettere la batch dimension come prima dimensione

In order to specify which behaviour a layer should have, the generic nn.Module class includes the train() and eval() methods, which alter a layer's behaviour accordingly.
For most layers (e.g. nn.Linear), there is no difference between the two modes, but for layers such as Dropout2d and BatchNorm2d it is important to set the layer to the correct working modality.
In general, you can call train() and eval() on the full model, and it will take care of calling the corresponding functions on its layers and sub-modules.