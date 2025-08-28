## Gradient Descent

**Come funziona:**

Aggiorna i parametri calcolando il gradiente dell’intera funzione di costo rispetto a tutti i dati.

**Formula:**

$$

\theta = \theta - \eta \cdot \nabla_\theta J(\theta)

$$

**Pro:**

- Semplice e intuitivo
- Converge al minimo globale per funzioni convesse
- Facile da implementare

**Contro:**

- Lento su dataset grandi
- Sensibile al learning rate
- Rischia di bloccarsi in minimi locali

**Quando usarlo:**

Ideale per piccoli dataset.

---

## Stochastic Gradient Descent (SGD)

**Come funziona:**

Aggiorna i parametri per ogni singolo esempio (o piccolo sottoinsieme).

**Formula:**

$$

\theta = \theta - \eta \cdot \nabla_\theta J(\theta; x^{(i)}, y^{(i)})

$$

**Pro:**

- Efficiente su grandi dataset
- Può uscire da minimi locali
- Facile implementazione

**Contro:**

- Alta varianza negli aggiornamenti
- Possibile instabilità

**Quando usarlo:**

Apprendimento online e real-time.

---

## Mini-batch Gradient Descent

**Come funziona:**

Usa piccoli batch di dati per calcolare il gradiente medio.

**Formula:**

$$

\theta = \theta - \eta \cdot \frac{1}{m} \sum_{i=1}^{m} \nabla_\theta J(\theta; x^{(i)}, y^{(i)})

$$

**Pro:**

- Buon compromesso tra efficienza e stabilità
- Ottimale per GPU

**Contro:**

- Richiede tuning del batch size

**Quando usarlo:**

Approccio standard per deep learning.

---

## AdaGrad

**Come funziona:**

Adatta il learning rate in base alla somma cumulativa dei gradienti al quadrato.

**Formule:**

$$

G_t = G_{t-1} + \nabla_\theta J(\theta)^2

$$

$$ \theta = \theta - \frac{\eta}{\sqrt{G_t + \epsilon}} \cdot \nabla_\theta J(\theta) $$

**Pro:**

- Buono per dati sparsi
- Learning rate adattivo

**Contro:**

- Learning rate può diventare troppo piccolo

**Quando usarlo:**

Modelli con dati sparsi o ad alta dimensionalità.

---

## RMSprop

**Come funziona:**

Usa media esponenziale dei quadrati dei gradienti.

**Formule:**

$$

E[g^2]t = \gamma E[g^2]{t-1} + (1 - \gamma) \nabla_\theta J(\theta)^2

$$

$$ \theta = \theta - \frac{\eta}{\sqrt{E[g^2]t + \epsilon}} \cdot \nabla\theta J(\theta)

$$

**Pro:**

- Stabile su problemi non stazionari
- Utile per RNN

**Contro:**

- Richiede tuning del decay rate

**Quando usarlo:**

Reti ricorrenti e problemi rumorosi.

---

## AdaDelta

**Come funziona:**

Elimina la necessità di un learning rate fisso usando un'unità di aggiornamento media mobile.

**Formule:**

$$

E[g^2]t = \rho E[g^2]{t-1} + (1 - \rho) \nabla_\theta J(\theta)^2 $$

$$

\Delta \theta_t = - \frac{\sqrt{E[\Delta \theta^2]_{t-1} + \epsilon}}{\sqrt{E[g^2]t + \epsilon}} \cdot \nabla\theta J(\theta) $$

$$

E[\Delta \theta^2]t = \rho E[\Delta \theta^2]{t-1} + (1 - \rho) (\Delta \theta_t)^2

$$

**Pro:**

- Nessun learning rate fisso
- Migliore gestione di gradienti sparsi

**Contro:**

- Convergenza iniziale lenta

**Quando usarlo:**

Quando si vogliono evitare iperparametri espliciti.

---

## Adam

**Come funziona:**

Combina momento (media dei gradienti) e RMSprop (media dei quadrati dei gradienti), con correzioni del bias.

**Formule:**

$$

m_t = \beta_1 m_{t-1} + (1 - \beta_1) \nabla_\theta J(\theta) \\ v_t = \beta_2 v_{t-1} + (1 - \beta_2) (\nabla_\theta J(\theta))^2 $$

$$ \hat{m}_t = \frac{m_t}{1 - \beta_1^t} \quad \hat{v}_t = \frac{v_t}{1 - \beta_2^t} $$

$$ \theta = \theta - \eta \cdot \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon}

$$

**Pro:**

- Rapida convergenza
- Adattivo e robusto

**Contro:**

- Potenziale overfitting
- Più costoso computazionalmente

**Quando usarlo:**

Modelli complessi, grandi dataset.

---

## AdamW

**Come funziona:**

Estende Adam con regolarizzazione L2 (weight decay) separata dal learning rate.

**Formule:**

$$

\theta = \theta - \eta \left( \frac{\hat{m}_t}{\sqrt{\hat{v}_t} + \epsilon} + \lambda \theta \right)

$$

**Pro:**

- Migliore generalizzazione
- Minore overfitting rispetto ad Adam

**Contro:**

- Richiede tuning extra (λ)

**Quando usarlo:**

Fine-tuning di modelli pre-addestrati, training con forte regolarizzazione.

#dpKnowledge 