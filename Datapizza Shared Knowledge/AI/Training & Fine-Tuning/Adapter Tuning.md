
**Adapter Tuning** è una tecnica di _Parameter-Efficient Fine-Tuning (PEFT)_ che consente di adattare un modello pre-addestrato a nuovi compiti aggiornando solo un piccolo numero di parametri aggiuntivi, chiamati **adapter**.

Invece di modificare tutti i pesi del modello, si **inseriscono piccoli moduli** (detti ****_layer_) tra i layer esistenti dell'architettura (tipicamente Transformer). Solo questi moduli vengono addestrati, mentre il resto del modello rimane congelato.

---

## Vantaggi principali

- **Efficienza computazionale**: meno parametri da aggiornare = meno memoria e tempo di addestramento
- **Modularità**: è possibile usare diversi adapter per diversi compiti, riutilizzando lo stesso modello base
- **Compatibilità**: evita il rischio di Catastrophic Forgetting perché non si alterano i pesi originali

---

## Come funziona

1. Si inseriscono **adapter layers** tra i layer di trasformazione del modello (es. dopo l'attention o dopo il feed-forward).
2. Ogni adapter è una **piccola rete feed-forward a bottleneck**:
    - Proietta la dimensione alta del modello a una dimensione più bassa
    - Applica una non-linearità (es. ReLU)
    - Riporta la dimensione bassa a quella originale.
3. Durante il fine-tuning, **si aggiornano solo i parametri degli adapter**.

(SX: dove sono collocati gli adapter nel Transformer, DX: architettura di un singolo Adapter)

![[Pasted image 20250827111116.png]]

#dpKnowledge 