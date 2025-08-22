La **distribuzione gaussiana**, detta anche **distribuzione normale**, è una delle distribuzioni di probabilità più importanti in statistica e machine learning. Descrive fenomeni naturali e processi aleatori centrati intorno a un valore medio.
## Definizione

La distribuzione gaussiana è definita da due parametri:

- **μ (mu)**: la media
- **σ (sigma)**: la deviazione standard

La sua funzione di densità di probabilità (PDF) è:

$$

f(x) = \frac{1}{\sqrt{2 \pi \sigma^2}} \exp\left( -\frac{(x - \mu)^2}{2 \sigma^2} \right)

$$

## Proprietà principali

- **Simmetrica** rispetto alla media μ
- La media, mediana e moda coincidono
- Il 68% dei dati cade entro 1 σ dalla media
- Il 95% dei dati cade entro 2 σ dalla media
- Il 99.7% dei dati cade entro 3 σ (ecco spiegato il famoso tre _sigma_) dalla media (regola 68-95-99.7)

## Parametri

|Parametro|Significato|
|---|---|
|μ|Media della distribuzione|
|σ|Deviazione standard|
|σ²|Varianza|

## Esempio

Supponiamo di avere una distribuzione gaussiana con:

- μ = 0
- σ = 1

La funzione diventa:

$$

f(x) = \frac{1}{\sqrt{2\pi}} \exp\left( -\frac{x^2}{2} \right)

$$

Questa è la **distribuzione normale standard**.

## Usi comuni

- Modellazione di errori di misura
- Inizializzazione dei pesi nelle reti neurali
- Filtraggio di rumore nei segnali
- Statistiche inferenziali (test Z)

## Relazioni

- La somma di variabili indipendenti tende alla normale (Teorema del limite centrale)
- La normale è una distribuzione di massima entropia per media e varianza fissate
#dpKnowledge 