# OIDC (**OpenID Connect)**

**OIDC** è un **sistema di login** sicuro e moderno che permette a un'applicazione (es. un sito web o un'app) di sapere **chi sei** grazie a un sistema di identità esterno.

È come dire:

> "Ehi, io non ti conosco, ma se Google dice che sei davvero tu, allora ti faccio entrare."

OIDC si basa su un altro protocollo chiamato **OAuth 2.0**, ma aggiunge in più l’identità (nome, email, ecc.).

---

## I protagonisti

1. **Identity Provider (IdP)**
    
    È il sistema che sa chi sei. Può essere Google, Facebook, o un sistema interno della tua azienda.
    
    ➜ Ti autentica (ti fa il login) e rilascia le informazioni su di te.
    
2. **Service Provider (o Relying Party)**
    
    È il sito o servizio che vuoi usare.
    
    ➜ Vuole sapere chi sei (ma NON gestisce direttamente il login). Si affida all’IdP.
    

---

## Come comunicano? Con i **token**

Una volta che fai il login con l’IdP, questo ti dà un **token**.

- Il **token** è un piccolo "biglietto" digitale che contiene informazioni su di te.
- Di solito è un **JWT (JSON Web Token)**: un file sicuro, firmato, che può contenere il tuo nome, email, scadenza, ecc.
- A volte viene **cifrato** (JWE) se serve più sicurezza.

Il token viene poi mandato al Service Provider, che lo legge e dice:

> "OK, questo token è valido. Benvenuto, puoi usare il mio servizio!"

---

## Due modalità d'uso: Client e Server

### Client (Frontend)

- Usi una **pagina di login**, inserisci username e password.
- Dopo il login, ricevi un **JWT** direttamente nel browser.
- Il browser lo usa per accedere ai dati nel frontend.
- Es: login con Google su un sito web → ricevi il token, il sito ti lascia accedere.

### Server (Backend)

- Il server comunica con l'IdP usando una **client_id** e una **client_secret** (una specie di username e password dell'app).
- L’utente non vede il token: viene gestito tutto sul server.
- Più sicuro, ideale per API o sistemi backend-to-backend.
- Es: un’app che deve autenticare un utente in background, senza che questo interagisca direttamente.

---

## Riassunto semplice

|Ruolo|Cosa fa|
|---|---|
|Identity Provider|Fa il login, conosce chi sei|
|Service Provider|Ti dà accesso, **se** l’IdP dice che sei tu|
|Token (JWT)|È il "pass" che ti permette di entrare|
|Client-side|Il browser gestisce tutto|
|Server-side|Il backend gestisce tutto, più sicuro|

#dpKnowledge 