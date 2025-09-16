**REST (Representational State Transfer)** è uno stile per progettare web API che tratta tutto come una **risorsa** (come utenti, post, prodotti) e usa i metodi HTTP standard per interagire con esse:

- `GET` → recupera una risorsa
- `POST` → crea una nuova risorsa
- `PUT/PATCH` → aggiorna una risorsa
- `DELETE` → elimina una risorsa (usato raramente, quando vuoi davvero cancellare qualcosa?)

**Le rotte sono strutturate usando nomi, non verbi**:

Esempio: `GET /users/123` recupera l’utente con ID 123, non `GET /getUser`.

REST incoraggia API pulite, prevedibili e stateless. Cerca di progettare le tue API seguendo il pattern REST.

#dpKnowledge 