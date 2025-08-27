Gli **Instruction-Tuned Models** sono modelli derivati dai foundation models, ottimizzati tramite **Supervised Fine-Tuning (SFT)** per seguire istruzioni date in linguaggio naturale.
SFT è quindi una fase in cui il modello impara a **seguire istruzioni** e **conversare** usando dati etichettati (es. domanda → risposta).

## Dataset:

```json
{
  "prompt": "Scrivi una poesia su un drago",
  "completion": "Il drago vola tra i cieli, sputando fuoco e stelle..."
}

```
![[Screenshot 2025-08-14 at 11.45.58.png]]
## Caratteristiche principali

- Allenati su coppie **prompt → risposta desiderata**.
- Migliorano notevolmente nella comprensione di comandi e domande.
- Ancora limitati in casi ambigui o complessi.

## Esempi noti

- `text-davinci-001`
- `FLAN-T5`
- `ChatGLM-SFT`

## Tecniche usate

- Supervised fine-tuning con annotazioni umane.
- Dataset costruiti manualmente o con modelli guida.
- Ottimizzazione del modello su output “corretti”, ma non ancora “preferibili”.

## Limiti

- Non sempre sicuri o ben allineati.
- Possono imitare male risposte ambigue.
- Mancanza di preferenza tra alternative valide.

Essendo SFT del Fine Tuning applicato in una fase temporale specifica dello sviluppo del modello, le tipologie di SFT corrispondono a quelle di fine tuning classico.

#dpKnowledge 