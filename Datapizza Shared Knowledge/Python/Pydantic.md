Utilizzato per validare i tipi, sia di input che di output. Ottimo per validare le strutture dei dizionari.

## Esempio semplice

Ho un dizionario che rappresenta un utente che va validato:

```python
{
    name:"Datapizza employee",
    age:25,
    email:"employee@datapizza.com>",
    nickname:"Enzo"
}
```

La relativa classe `models.py` può essere:

```python
from pydantic import BaseModel, EmailStr, Field
from typing import Optional

class User(BaseModel):
    name: str = Field(..., min_length=2, max_length=50)
    age: int = Field(..., ge=0, le=120)  # età tra 0 e 120
    email: EmailStr
    nickname: Optional[str] = None
```

File di validazione:

```python
# Esempio corretto
user = {
    name="Datapizza employee",
    age=25,
    email="employee@datapizza.com",
    nickname="Enzo"
)
# unpacking dei campi
user = User(**user)
print(user)

# Esempio con errore di validazione
invalid_user = {
        name:"A",            # troppo corto
        age:-5,              # età negativa
        email:""not-an-email" # email non valida
    }
try:
    invalid_user = User(**invalid_user)
except Exception as e:
    print("Errore di validazione:", e)

```

## Esempio avanzato da ABB Policy Checker

Ho un dizionario di output che va validato:

```python
{
	'commercial_conditions_details': {
		commercial_condition_id: {
			'filename': { #(just put the placeholder 'filename')
				'extracted_information': {
					'information_content':
					'information_page': [page_number or list of page numbers]
					#IMPORTANT: value or values must always be within a list AND each page number MUST be a string
					},

				'checking_result':
				'comment':
			},
		}
	}
}

```

La relativa classe Pydantic `ai_models.py` può essere:

```python
class ExtractedInformation(BaseModel):
    """Contiene l'informazione estratta dal documento."""

    information_content: str = Field(
        ..., description="Il testo esatto estratto dal documento."
    )
    information_page: List[str] = Field(
        ..., description="La pagina da cui è stato estratto il testo."
    )

class FileAnalysisResult(BaseModel):
    """Il risultato dell'analisi per un singolo file."""

    extracted_information: Optional[ExtractedInformation] = Field(
        None, description="L'informazione estratta dal documento."
    )
    checking_result: str = Field(
        ..., description="L'esito del controllo (es. 'Compliant', 'Not Compliant')."
    )
    comment: str = Field(
        ..., description="Un commento dell'AI che motiva il risultato."
    )

class CommercialConditionResult(BaseModel):
    """Il risultato dell'analisi per una singola condizione commerciale."""

    filename: FileAnalysisResult

# Questo è il modello principale che ci aspettiamo dall'output di un agente
class AnalysisOutputSchema(BaseModel):
    """
    Rappresenta l'output JSON completo di un agente,
    contenente i dettagli per ogni condizione commerciale analizzata.
    """

	# IMPORTANTE QUESTO PATTERN, se non conosci le chiavi già a coding time
    commercial_conditions_details: Dict[str, CommercialConditionResult] = Field(
        ...,
        description="Un dizionario dove la chiave è l'ID della condizione e il valore è un altro dizionario con chiave il nome del file.",
    )

```

File su cui fai la validazione:

```python
try:
	result = await self.a_invoke(document_content)

	# Parse JSON
	parsed_json = json.loads(result)

	# Validate with pydantic
	try:
		validated_response = AnalysisOutputSchema(**parsed_json)
		old_dict["commercial_conditions_details"] = (
			validated_response.commercial_conditions_details
		)
		logger.info(
			f"Successfully validated agent response on attempt {attempt + 1}"
		)
		break

	except ValidationError as validation_error:
		logger.error(
			f"Pydantic validation failed on attempt {attempt + 1}/{max_retries}: {validation_error}"
		)
		logger.error(f"Raw result text: {result[:200]}...")

		if attempt == max_retries - 1:
			logger.error(
				"All retry attempts failed due to validation errors. Returning empty result."
			)
			return json.dumps(
				{
					"commercial_conditions_details": {},
					"error": f"Validation error after {max_retries} attempts: {str(validation_error)}",
				}
			)
		continue

```

HINTS:

- Ogni classe è come se fosse un dizionario.
- Se hai dizionari per cui le keys sono valori che sai solo a runtime (es. `commercial_condition_id`) allora usa `Dict[str,...]`, dato che in anticipo non puoi sapere il nome del campo.
- Una volta che fai il controllo, il dizionario conterrà ora dei valori della rispettiva classe perciò non ci puoi accedere con `[]` ma con `.` $\rightarrow$ se vuoi facilitare l'accesso con `[]`, usa `nuovo_dict.model_dumps()`.

#dpKnowledge 