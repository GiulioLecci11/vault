## Contesto e obiettivo del post
Il blogpost prova a rispondere in modo “interno” alla domanda: **come sta cambiando il lavoro con l’AI, quando l’AI è già usata ogni giorno da persone che la costruiscono?** Anthropic, in particolare, studia i propri ingegneri e ricercatori (cioè “early adopters”) per capire **benefici, nuove pratiche e tensioni** che potrebbero anticipare cosa vedremo altrove.

Per farlo, Anthropic combina tre fonti:
- **Survey (agosto 2025)** a **132** ingegneri e ricercatori.
- **53 interviste qualitative** approfondite.
- Analisi di **~200.000 transcript interni di Claude Code** (febbraio e agosto 2025) tramite uno strumento interno “privacy-preserving” chiamato **Clio**.  

---

## Key findings (in parole semplici)
Il post sostiene che l’AI (Claude) sta:
1. **Aumentando molto la produttività percepita** e soprattutto l’output.
2. Rendendo le persone più **“full-stack”** (capaci di agire anche fuori dal proprio dominio).
3. Spostando alcune attività da “scrittura di codice” a **supervisione, revisione e orchestrazione** del lavoro dell’AI.
4. Sbloccando una quota non banale di lavoro che **prima non si faceva affatto** (nice-to-have, tooling, “papercuts”).
5. Portando tensioni reali: **atrofia di competenze profonde**, “paradosso della supervisione”, **cambiamento della collaborazione** con i colleghi e **incertezza di carriera**.

---

## 1) Come Anthropic usa Claude: cosa emerge dai dati della survey

### Per cosa lo usano più spesso (task concreti)
Dalla survey, gli usi più frequenti sono:
- **Debugging / fixing errori** (molti lo usano quotidianamente).
- **Code understanding**: farsi spiegare porzioni di codebase, orientarsi velocemente.
- **Implementare nuove feature** (molto comune, anche se leggermente meno di debugging/understanding).

Usi meno frequenti (perché meno comuni o “tenuti più umani”):
- **High-level design / planning**
- **Data science**
- **Front-end**

### Quanto lo usano e che boost dichiarano
Le persone riportano:
- Claude in circa **~60% del lavoro**
- **~+50%** di produttività media percepita, con un aumento “2–3x” rispetto a un anno prima (quando riportavano ~28% di uso e ~+20% di boost).  
Il post sottolinea però che **la produttività auto-riportata è difficile da misurare**, e cita un lavoro esterno (METR) dove dev esperti su codebase familiari **sovrastimavano** il boost.

### Il pattern interessante: meno tempo… ma soprattutto più output
Un punto chiave: il guadagno non è solo “finisco prima”, ma spesso è:
- un po’ **meno tempo per categoria di task**
- e **molto più volume di output** (più fix, più refactor, più tooling, più iterazioni)

Inoltre, alcuni dichiarano addirittura **più tempo** su task AI-assisted: perché passano tempo a
- ripulire/aggiustare codice generato (“vibe code into a corner”),
- sostenere più carico cognitivo nel capire codice che non hanno scritto,
- oppure perché l’AI rende “possibile” perseverare su cose che prima avrebbero mollato e quindi finiscono per farle davvero.

---

## 2) Lavoro “nuovo” abilitato dall’AI (non solo accelerazione)
Uno dei risultati più informativi è che i dipendenti stimano che circa **27% del lavoro assistito da Claude “non sarebbe stato fatto” senza Claude**.

Esempi tipici di questo “nuovo lavoro”:
- **Scaling di progetti** (fare più varianti/esperimenti, provare più strade).
- **Nice-to-have**: dashboard interattive, strumenti interni, miglioramenti non prioritari.
- Lavori “utili ma tediosi” come **documentazione e testing**.
- **Exploratory work** che prima non era costo-efficace far “a mano”.

C’è anche una metafora concreta: non è solo come avere “un’auto più veloce”, ma come avere **“un milione di cavalli”**: la potenza sta nel **parallelismo** (provare molte opzioni, molte implementazioni o molte idee contemporaneamente).

---

## 3) Tecniche e criteri pratici: come decidono cosa delegare a Claude
Questa è una delle parti più operative del post: Anthropic riassume i criteri che gli ingegneri usano per delegare in modo produttivo.

### Cose che tendono a delegare (criteri ricorrenti)
1. **Bassa complessità e basso contesto**
   - Esempio: “ho poco contesto ma sembra una cosa semplice”.
   - Caso tipico: coprire “buchi” su tool/ambienti (Git/Linux) o aree non familiari.

2. **Facilmente verificabile**
   - Se la validazione è economica rispetto alla creazione, Claude è molto utile.
   - Esempio: output dove puoi fare “sniff-check” veloce.

3. **Ben definito / self-contained**
   - Subcomponenti decoupled, task a confini chiari.

4. **Low-stakes / qualità non critica**
   - Esempio: “throwaway debug o research code”.
   - Dove accetti imperfezioni purché arrivi rapidamente a un risultato.

5. **Ripetitivo o noioso**
   - Se il compito genera resistenza/procrastinazione, Claude riduce l’“attrito di avvio”.

6. **Quando è più veloce chiedere che fare**
   - Ma con un caveat: c’è un **“cold start problem”**.
   - Se per spiegare tutto il contesto al modello impieghi più che a farlo tu, spesso non conviene.

### “Trust but verify” e progressione della fiducia
Molti descrivono un percorso: iniziare con task semplici e poi delegare sempre più complesso.  
Il post usa l’analogia di **Google Maps**: prima ti fidi solo su percorsi ignoti, poi su quelli “quasi noti”, alla fine lo usi sempre e ti fidi se propone deviazioni.

### Dentro o fuori dalla propria expertise?
Qui emerge una spaccatura:
- Alcuni usano Claude **fuori dominio** (front-end per backend dev, tooling, ecc.) per estendere capacità.
- Altri preferiscono usarlo **dentro dominio** per poter validare meglio e guidare meglio.

Esempio concreto (molto importante): un security engineer parla di soluzioni “smart in modo pericoloso”, come un junior brillante che propone qualcosa che *sembra* giusto ma è rischioso: serve **giudizio** per riconoscere i bug “insidiosi”.

---

## 4) Quanto possono “delegare davvero” senza supervisione?
Nonostante l’uso frequente, la maggioranza dice di poter **“delegare completamente” solo 0–20% del lavoro**.

Messaggio chiave: Claude è spesso un **collaboratore costante**, ma la modalità dominante è:
- lavoro iterativo con AI
- **supervisione e validazione**, soprattutto su lavoro ad alto impatto o dove gli standard di qualità sono stringenti

E qui nasce il tema che ritorna: l’AI non elimina automaticamente il lavoro umano, spesso lo **sposta**.

---

## 5) Trasformazione delle skill: “più ampiezza”, ma rischio di perdita di profondità

### Espansione: diventare più “full-stack”
Claude rende comuni cose prima rare:
- backend dev che fanno UI,
- ricercatori che creano visualizzazioni,
- team non “core engineering” che sbloccano debug, scripting, data task.

Esempio vivido: un backend engineer racconta di aver costruito una UI complessa con Claude, e i designer stupiti: “l’hai fatto tu?” — “No, l’ha fatto Claude, io ho promptato.”

Effetto sistemico: **feedback loop più stretti**.
Un processo di “settimane” (build → meeting → iterazioni) può diventare “ore” con feedback live.

### Rischio: meno pratica hands-on e atrofia
Molti temono:
- perdita del “collateral learning” (imparare leggendo doc, facendo tentativi, esplorando il sistema)
- dipendenza dall’AI per usare tool nuovi, con meno competenza interna sedimentata

Esempio concettuale:
- prima, debuggare duramente ti costringeva a costruire un modello del sistema;
- ora Claude ti porta “subito” al punto, ma salti il percorso di apprendimento.

### Il “paradosso della supervisione”
È un punto centrale del post:
- per usare bene Claude serve supervisione,
- ma per supervisionare serve competenza tecnica,
- e l’uso intenso di Claude può ridurre proprio quella competenza.

Contromisura pratica: alcuni dichiarano di **non usare Claude intenzionalmente** a volte, anche se “potrebbe farlo”, per restare “sharp”.

### La grande domanda: serviranno ancora quelle skill?
Il post paragona la transizione ad altre astrazioni storiche (assembly → linguaggi high-level):
- forse “English as a programming language” e “vibe coding” sono solo un nuovo livello di astrazione,
- ma ogni astrazione ha un costo (come la perdita della comprensione profonda della memoria, per analogia).

Gli ingegneri sono divisi:
- alcuni non temono l’erosione,
- altri la accettano come trade-off (“non mi serve più”),
- un altro commento interessante: parlare di “ruggine” presuppone che si torni al pre-AI, e loro non credono accadrà.

---

## 6) Il senso del “craft” cambia: identità e soddisfazione
Qui emerge un tema umano, non solo tecnico:
- alcuni vivono perdita reale (anni di identità professionale legata alla scrittura di codice),
- altri preferiscono risultati e impatto, non il “fare codice” in sé,
- altri ancora trovano che “promptare tutto il giorno” non sia gratificante come entrare in flow e costruire.

In sostanza, l’AI sposta la domanda da:
> “Ti piace programmare?”
a
> “Che cosa ti dava il programmare, e puoi ottenerlo anche così?”

---

## 7) Dinamiche sociali: Claude come “primo punto di contatto”
Una tesi forte: per molte domande Claude sostituisce i colleghi come primo step.

Esempi:
- qualcuno dice che **80–90% delle domande** ora le fa a Claude.
- effetto “filtro”: Claude gestisce routine e chiarimenti; i colleghi restano per questioni ad alto contesto o strategiche (“l’ultimo 20% è cruciale”).

Impatto su mentorship:
- meno junior che vanno dai senior,
- più coaching “on-demand” via AI,
- alcuni senior lo vivono con tristezza perché perdono occasioni di mentoring, anche se riconoscono che i junior risolvono più velocemente.

---

## 8) Evoluzione della carriera: da “writer” a “manager di agenti” (e l’ansia sul lungo termine)
Molti descrivono lo shift verso:
- gestione di più istanze di Claude
- lavoro da “editor / reviewer / reviser”
- responsabilità di output prodotto da “1, 5 o 100 Claudes”

Ma insieme a questo c’è una **incertezza di fondo**:
- alcuni esplicitano il timore di “automatizzarsi fuori dal lavoro”,
- altri restano ottimisti ma preoccupati per i junior,
- molti dicono “non lo so”, e puntano sull’adattabilità come unica strategia robusta.

---

## 9) Dati “osservati” dai transcript di Claude Code: più autonomia e task più complessi
La parte quantitativa su Claude Code prova a corroborare la survey con comportamento reale.

Tra febbraio e agosto 2025, Anthropic riporta:
- **aumento della complessità media dei task** (scala 1–5, da ~3.2 a ~3.8)
- **più catene di azioni autonome** per transcript (da ~9.8 a ~21.2 tool calls consecutive; +116%)
- **meno turni umani** (da ~6.2 a ~4.1; -33%)

Interpretazione: Claude richiede meno steering, gestisce workflow più lunghi.

### Shift nella distribuzione dei task
Cambiamento più marcato:
- **Implementare nuove feature** cresce molto come quota (da ~14% a ~37%)
- **Design/planning** aumenta (da ~1% a ~10%)

Il post è prudente: questo può significare che l’AI è migliorata su task complessi, ma può anche essere un cambio di adoption/workflow (non misurano il volume assoluto).

### “Papercuts” (il micro-miglioramento che fa accumulo)
Una categoria rilevante:
- **8,6%** dei task sono “papercut fixes”: refactor per manutenibilità, shortcut, piccoli tooling, strumenti di visualizzazione performance…

Messaggio: queste cose prima si rimandavano, ora diventano fattibili e nel tempo possono aumentare efficienza e ridurre frizione.

### Differenze per team (esempi)
- **Pre-training**: molto “feature building” (spesso esperimenti extra).
- **Alignment & Safety / Post-training**: più front-end per visualizzazioni dati.
- **Security**: tantissimo code understanding (implicazioni di sicurezza).
- **Non-technical employees**: molto debugging (es. network/Git) e anche data science—Claude come “ponte” di competenze.

---

## 10) “Looking forward”: cosa dice Anthropic che farà
Il post non dà “ricette definitive”, ma indica che Anthropic sta:
- parlando con team e leadership su come gestire opportunità e rischi,
- riflettendo su **collaborazione** e **sviluppo professionale**,
- tentando di definire best practice per lavoro AI-augmentato (citano un “AI fluency framework”),
- espandendo la ricerca oltre gli ingegneri,
- supportando soggetti esterni (es. CodePath) per adattare curricula,
- considerando approcci strutturali futuri (reskilling, evoluzione ruoli).

---

## Limiti e caveat (detto in modo utile)
Il post è abbastanza trasparente sui limiti:
- campione non perfettamente casuale (convenience + outreach mirato) e possibile **selection bias** (rispondono più i motivati o con opinioni forti)
- **social desirability** (non anonimo, e sono dipendenti)
- **recency bias** nel ricordare “12 mesi fa”
- la produttività è intrinsecamente difficile da stimare
- nell’analisi dei transcript misurano **cambiamenti relativi** (quote), non volume assoluto
- risultati legati allo “stato dell’arte” di agosto 2025 (modelli citati: Sonnet 4 e Opus 4), e potrebbero già essere cambiati

---

## Sintesi finale (il “senso” del post)
Anthropic descrive un’organizzazione in cui l’AI non è una feature accessoria ma un **collaboratore quotidiano**. Il cambiamento principale non è solo “fare le stesse cose più velocemente”, ma:
- **fare più cose** (output/volume),
- fare cose prima “non conveniente” (tooling, papercuts, nice-to-have),
- **espandere** il raggio d’azione delle persone (più full-stack),
- e contemporaneamente affrontare nuove fragilità: supervisione, perdita di skill profonde, mentorship, significato del lavoro e ansia sul futuro.
