**PEFT** (_Parameter-Efficient Fine-Tuning_) è un insieme di tecniche per **adattare** modelli di grandi dimensioni (come i Large Language Models) a compiti specifici, riducendo drasticamente il numero di parametri da aggiornare durante l'addestramento.

Queste tecniche permettono di mantenere le prestazioni elevate del modello base, risparmiando risorse computazionali e spazio di memoria.

## Vantaggi di PEFT

- Riduzione della probabilità di [Catastrophic Forgetting](https://www.notion.so/Catastrophic-Forgetting-24e04d204fa280f78b4be7b3f4b53df6?pvs=21)
- Riduzione del costo computazionale e della memoria
- Maggiore efficienza nel deploy su hardware limitato
- Possibilità di riutilizzare il modello base per più task con piccoli adapter specifici
- Ottimo in situazioni in cui il dataset è **ridotto** (PEFT approaches have also shown to be better than fine-tuning in the low-data regimes and generalize better to out-of-domain scenarios. It can be applied to various modalities, e.g., image classification and stable diffusion dreambooth.)

![[Pasted image 20250827111935.png]]
## Principali tecniche di PEFT

|Nome|Cosa viene modificato|Pro|Contro|
|---|---|---|---|
|[Adapter Tuning](https://www.notion.so/Adapter-Tuning-24e04d204fa28047af25d894e14e68a5?pvs=21)|Solo i parametri dei moduli adapter aggiunti|Modularità; isolabile per task; facile da gestire|Maggiore latenza/inferenza; aumenta profondità|
|[LoRA](https://www.notion.so/LoRA-24e04d204fa2801584f0e13f083bbc34?pvs=21)|Solo le matrici a rango ridotto (fattori low-rank)|Leggero; merge-abile; nessun overhead in inferenza|Più limitato per compiti molto diversi|
|Prompt Tuning|Solo i prompt learnable (vettori di embedding)|Estremamente leggero; indipendente dall’architettura|Richiede prompt molto lunghi su task complessi|
|Prefix Tuning|Solo i prefix learnable nei layer di attenzione|Migliora rispetto a prompt tuning; più espressivo|Aumenta un po’ la complessità in inferenza|
|P-Tuning v2|Prompt appresi più stabili e ottimizzati|Più stabile/scalabile di prompt tuning; più efficace|Più difficile da implementare di prompt tuning|
|BitFit|Solo i bias del modello|Estremamente semplice; pochissimi parametri|Limitata capacità di adattamento|

## Conclusioni

PEFT rappresenta un'area chiave per rendere i Large Language Models più accessibili, economici e personalizzabili per task specifici.

Queste tecniche stanno trovando sempre più applicazione nella pratica, soprattutto in contesti industriali o di ricerca dove le risorse computazionali sono limitate.

#dpKnowledge 