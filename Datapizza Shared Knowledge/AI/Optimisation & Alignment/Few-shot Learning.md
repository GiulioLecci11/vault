
Tecnica di apprendimento dove al modello vengono forniti alcuni esempi nel prompt per mostrargli il tipo di output desiderato. Usato in fase di inferenza, senza modificare i pesi del modello.

## Tipologie

### **Zero-shot Learning**

Il modello esegue un task senza esempi, solo con istruzioni.

```
Prompt: "Traduci in francese: Hello world"
Output: "Bonjour le monde"

```

### **One-shot Learning**

Un singolo esempio per mostrare il pattern desiderato.

```
Esempio: "happy -> felice"
Prompt: "Traduci in italiano: sad"
Output: "triste"

```

### **Few-shot Learning**

Multipli esempi (tipicamente 2-10) per stabilire il pattern.

```
Esempi:
Q: Qual è la capitale della Francia?
A: Parigi

Q: Qual è la capitale della Germania?
A: Berlino

Q: Qual è la capitale dell'Italia?
A: Roma

Q: Qual è la capitale della Spagna?
A: Madrid

```

## Vantaggi

- **Velocità**: Nessun training aggiuntivo richiesto
- **Flessibilità**: Adattabile a nuovi task immediatamente
- **Economico**: Non serve riaddestramento costoso
- **Generalizzazione**: Funziona su domini non visti prima

## Limitazioni

- **Dipendenza dal prompt**: La qualità dell'esempio è cruciale
- **Context length**: Limitato dalla lunghezza massima del prompt
- **Consistenza**: Può essere meno stabile del fine-tuning
- **Performance**: Spesso inferiore a modelli specializzati

## Strategie per Migliorare Few-shot

### 1. **Chain-of-Thought Prompting**

```
Esempio:
Q: Marco ha 15 mele. Ne mangia 3 e ne regala 5. Quante ne rimangono?
A: Marco inizia con 15 mele. Ne mangia 3, quindi: 15 - 3 = 12.
   Poi ne regala 5, quindi: 12 - 5 = 7. Rimangono 7 mele.

Q: Sara ha 20 euro. Spende 8 euro per pranzo e 3 euro per il caffè. Quanto le rimane?
A: ?

```

### 2. **Diversificazione degli Esempi**

Usa esempi che coprano:

- Casi semplici e complessi
- Variazioni stilistiche
- Edge cases possibili

### 3. **Ordine degli Esempi**

- Metti esempi più semplici prima
- Termina con il caso più simile al target


#dpKnowledge 