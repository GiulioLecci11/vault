Machine Learning  (si parla di AI statistica e non deterministica)
Modelli statistici che vengono istruiti a compiere uno specifico compito tramite un processo chiamato “addestramento” (training) nel quale vedono milioni di esempi del compito che devono imparare.

Il Deep Learning tradizionale non genera nuovi dati, ma li etichetta e li classifica, oppure fa una previsione numerica.

Generative AI (GenAI)
Una sotto-branca del Deep Learning, che si occupa della generazione di nuovi dati. Possono essere testuali, audio, video, o di qualsiasi altro tipo. 

Large Language Model (LLM) (In genere si dice che un modello è “Large” quando supera i 50 miliardi di parametri -> 50T)
Viene chiamata così la più popolare famiglia di modelli di AI generativa, addestrati per comprendere il linguaggio ed eseguire i nostri comandi (”prompt”).
Alcuni di questi modelli sono detti “multi-modali”, Large Multimodal Models (LMM). 

Un modello si definisce MULTIMODALE quando può ricevere in input non solo testo ma dati di vario tipo come testo, immagini, audio ecc..
Attenzione che ChatGPT è l’applicazione web (un chatbot) che offre un’interfaccia al modello GPT sottostante, che è un modello di linguaggio. 

Nel mondo open source i player più attivi sono Meta e Mistral, con modelli avanzati che spesso battono alcuni dei modelli commerciali “chiusi”.

DOVE STA IL MODELLO?
I modelli GenAI di linguaggio sono, in pratica, file che contengono numeri, ossia i valori dei “neuroni” del modello, rappresentati dalla rete neurale Transformer.
Spesso il file del modello contiene anche dei metadati che descrivono come questi numeri sono collegati fra di loro, dando informazioni sull’architettura del modello (quanti strati ha la rete, come sono collegati gli strati, ecc.). 
Quindi nella pratica un modello di GenAI è un file unico, dal peso variabile (tra qualche centinaio di MB, a centinaia di GB per i modelli più grandi) e viene ospitato in un cloud (ad esempio i modelli di OpenAI stanno su Azure, AWS ospita i suoi ecc..).
Mentre modelli più leggeri come quelli opensource possono stare anche in locale.


COME SI ACCEDE AL MODELLO?
1) Chatbot: tramite applicazione web, come chatgpt o Claude (modelli di Anthropic).
2) API: come i modelli vengono richiamati dagli sviluppatori che costruiscono applicazioni usando l'AI.

COME SI ALLENANO QUESTI MODELLI?
Solitamente si sfrutta un insieme di unsupervised, supervised e infine reinforcement learning. Sono necessari tantissimi dati (OpenAI ha sfruttato CommonCrawl: un dataset pubblico che è stato costruito grazie a degli “scraper”, ovvero dei bot che girano per Internet e salvano tutte le pagine web che trovato (oltre a dati che non conosciamo) ), tanta potenza di calcolo (OpenAI ha affittato migliaia di GPU Nvidia) e mesi di tempo.

Dopo il training intensivo sulle GPU gli si deve "insegnare a conversare" e qui entra in gioco il reinforcement learning, in particolare questo ultimo step si chiama <<RLHF>>, Reinforcement Learning from Human Feedback (Di base infatti questi modelli sono detti “instruct model”, perché nel dataset di addestramento non ci sono conversazioni ma solo “comandi singoli”.La fase di RLHF serve per prendere rendere un “instruct model” → un “chat model”), e prevede che una squadra di umani valuti l’output del modello su vari aspetti come completezza correttezza e utilità, rispetto a vari prompt dati in input. Infine si riallena il modello sui feedback raccolti.
In questa fase si impongono anche i "guardrails" che sono limitazioni alle risposte del modello da un punto di vista etico e morale.
Tuttavia questa fase ha anche degli aspetti controproducenti:Si ipotizza che il problema delle allucinazioni sia peggiorato dal modo in cui vengono addestrate a conversare le AI dopo il training, ovvero la fase di RLHF.

In questa fase, l’AI chiede feedback agli umani sulla bontà del risultato, ma il problema è che gli umani potrebbero sbagliare nel dare il feedback, oppure preferire una risposta che “suona come la più corretta” ma che non per forza lo è.

Nella pratica i chatbot che usano modelli di GenAI, come ChatGPT o Gemini, sono ulteriormente addestrati a “soddisfare” al meglio l’utente, creando la frase più “utile per l’umano che la sta valutando”.

Ma non è detto che la frase più utile sia quella corretta.

Distillation:
Tecnica di transfer learning dove si utilizza un modello <teacher> per allenarne uno <student> solitamente più piccolo e veloce da utilizzare ed allenare. Il training consiste nell'utilizzare l'output del modello teacher come truth model per lo student.



ALTRI STRUMENTI AI:
Wrapper AI:
Un prodotto che usa l’AI generativa tramite modelli ospitati da terze parti. Il prodotto usa le chiamate API per connettersi ai modelli (come i GPT di OpenAI) ed eseguire funzionalità avanzate, senza che l’azienda debba avere l’expertise AI o le risorse per addestrare un modello proprietario. 

Ad esempio perplexity.ai o character.ai sono due wrapper che si connettono alle API di OpenAI, Anthropic, e altri, per funzionare. 

Perplexity sta anche addestrando dei modelli interni ora, ma in genere conviene affidarsi ai modelli AI delle grandi aziende, perché risultano essere più performanti.

AI Agent:
Autonomous Function che utilizza l'AI per prendere delle decisioni ed effettuare dei task. Un esempio potrebbe essere un automated email manager che quando riceviamo una mail ne analizzi il testo ed il contesto e può evidenziare le informazioni importanti, preparare riassunti o addirittura se dotato delle giuste info, rispondere alla mail in maniera del tutto autonoma.


LIMITI AI:
1) Hallucinations: date dal fatto che la genAI non ragiona, mai. Ma bensì restituisce sempre quello che sarà l'output più probabile al prompt che è stato presentato in input. Questo può generare errori ad esempio nei calcoli numerici, poiché i numeri vengono trattati come stringhe e MAI come numeri, non viene svolto NESSUN calcolo dall'IA. (Per aggirare il problema puoi usare l’AI per scrivere codice (ad esempio, Python), che può invece eseguire il calcolo corretto.)
2) Tempo di risposta: L'IA si sforzerà per fornire una risposta il più velocemente possibile, senza effettivamente ragionare sul problema a meno che non siamo noi a "imporglielo" tramite prompting. Ultimamente si stanno sviluppando espedienti come le chain of thought proprio per questo.



COME DIVENTARE AI ENGINEER 
https://topaz-polyanthus-5ac.notion.site/Come-diventare-GenAI-Engineer-b56621bba6184188a2b3cbb9b9ebfd06
#AI 