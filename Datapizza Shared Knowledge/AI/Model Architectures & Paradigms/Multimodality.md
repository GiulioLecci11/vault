Multimodalità indica la capacità di un modello di intelligenza artificiale di accettare input da più modalità di dati, come testo, immagini, audio o video. I modelli multimodali permettono interazioni più ricche e sono usati per compiti complessi come il riconoscimento visivo, la comprensione contestuale, la generazione di immagini da testo e altro.

Esempi:

- GPT-4V (vision)
- Gemini di Google

Esempi di codice:

```python
response = client.chat.completions.create(
model="gpt-4o",
messages=[
{
"role": "user",
"content": [{"type": "text", "text": "Cosa c'è in questa immagine?"},
{
"type": "image_url",
"image_url": {
"url": "<https://www.chinese-forums.com/uploads/monthly_2018_11/1223A182-7198-493D-B57A-E569E6D1CBC7.jpeg.7fef77700334d3030c772d091d3b5328.jpeg>",
},},],}],
max_tokens=300,
)

```

o con file audio

```python
response = client.chat.completions.create(
model="gpt-4o-audio-preview",
messages=[
{
"role": "user",
"content": [{"type": "text", "text": "Rispondi alla richiesta."},
{
"type": "input_audio",
"input_audio": {"data": encoded_string,"format": "wav"}}]
},])

```

#dpKnowledge 