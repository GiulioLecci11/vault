Un agente per sezione/documento (decido io la granularità). Ogni agente risponde con gli id dei suoi chunk che ritiene rilevanti → lista di `chunk_ids`.

### Input

- Ogni agente ottiene in input un documento già chunkato, quindi una serie di chunk come dizionari. Nella foto che segue c'è la KB chunkata!
    
    ![[Pasted image 20250904092017.png]]

### Procedimento

1. Ogni agente si occupa per ogni documento di retrievare gli indici dei chunk utili, caricandoli nel contesto
2. Il master riceve gli id dei chunk, fa retrieve e crea il testo per llm.

### Pro

- Migliora la ricerca
- É facilmente tunable

### Contro

- Costi elevati
- Limitata scalabilità
- Fattibile se i documenti originali sono ben strutturati, altrimenti difficile chunkare

Link repo: TODO

#dpKnowledge 