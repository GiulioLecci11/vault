L’addestramento di un LLM moderno si articola in più fasi distinte:

1. [[Raw Model]]
2. [[Pre-Training]]
3. [[Supervised Fine Tuning]]
4. [[Alignment (RLHF)]]

Ogni fase ha uno scopo preciso e richiede dati diversi.

![[Pasted image 20250827112608.png]]
Meme rappresentativo delle fasi di addestramento, in cui:

1. il self-supervised pre-training porta ad un modello che può essere considerato come un mostro ribelle che usa i dati di internet in modo indiscriminato
    
2. questo mostro viene finetunato su dati di alta qualità (es. Stack Overflow, Quora, annotazioni umane, ...) e questo lo rende più "socialmente accettabile"
    
3. questo modello finetunato viene ulteriormente raffinato usando il preference fine-tuning per renderlo _"customer-appropriate”_ e dargli una faccina sorridente :)
    ![[Pasted image 20250827112626.png]]


#dpKnowledge 