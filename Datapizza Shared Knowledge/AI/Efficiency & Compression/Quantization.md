Tecnica che riduce la precisione numerica dei singoli pesi della rete (es. da float32 a int8) per ridurre l'uso di memoria e velocizzare i calcoli.

## Analogia Semplice

Immagina di descrivere l'altezza delle persone:

- **Alta precisione**: "Marco è alto 1.783542 metri" (troppo dettagliato)
- **Bassa precisione**: "Marco è alto circa 1.8 metri" (più pratico)

La quantization fa la stessa cosa con i numeri nel modello AI: usa meno decimali mantenendo l'informazione essenziale.

## Come Funziona

### Precisioni Numeriche

```
float32 (32 bit): 1.234567890123456789 (precisione massima)
float16 (16 bit): 1.234567 (precisione media)
int8 (8 bit):     123 (precisione bassa)
int4 (4 bit):     12 (precisione minima)

```

### Processo di Quantization

```
Peso originale: 0.847291 (float32)
↓ Quantization a int8
Peso compresso: 847 (int8)
↓ Dequantization per calcolo
Peso approssimato: 0.847000 (float32)

Perdita: 0.000291 (trascurabile)
Risparmio memoria: 75%

```

## Esempi Pratici

### Modello GPT-7B

```
Originale (float32):
- Dimensione: 28 GB
- RAM richiesta: 32 GB
- Velocità: 50 token/sec

Quantized Q4 (4-bit):
- Dimensione: 3.5 GB
- RAM richiesta: 8 GB
- Velocità: 80 token/sec
- Accuratezza: -2% vs originale

Risultato: 8x meno memoria, più veloce, quasi stessa qualità

```

## Tipi di Quantization

### 1. Post-Training Quantization

```
Processo:
1. Addestra modello normale (float32)
2. Converti pesi a precisione minore
3. Testa performance

Pro: Semplice, veloce da applicare
Contro: Possibile perdita accuratezza

```

### 2. Quantization-Aware Training

```
Processo:
1. Durante training simula low-precision
2. Modello impara a compensare errori
3. Quantization finale più accurata

Pro: Miglior qualità finale
Contro: Training più complesso e lento

```

### 3. Dynamic Quantization

```
Durante inferenza:
- Pesi: sempre quantized (int8)
- Attivazioni: calcolate in float32
- Conversione dinamica quando serve

Pro: Bilanciamento qualità/velocità
Contro: Overhead conversioni

```

## Livelli di Compressione

### Q8 (8-bit)

```
Compressione: 4x meno memoria
Perdita qualità: <1%
Uso tipico: Produzione, quando serve massima qualità

Esempio:
- GPT-7B: 28 GB → 7 GB
- Accuratezza: 99% dell'originale

```

### Q4 (4-bit)

```
Compressione: 8x meno memoria
Perdita qualità: 2-5%
Uso tipico: Mobile, edge computing

Esempio:
- GPT-7B: 28 GB → 3.5 GB
- Accuratezza: 95% dell'originale

```

### Q2 (2-bit)

```
Compressione: 16x meno memoria
Perdita qualità: 10-20%
Uso tipico: Ricerca, casi estremi

Esempio:
- GPT-7B: 28 GB → 1.75 GB
- Accuratezza: 80-85% dell'originale

```

Esempio: Q4 significa 4 bit per parametro. Più compressione → meno accuratezza.

## Implementazione Pratica

### Con PyTorch

```python
import torch

# Modello originale
model = torch.load('model_float32.pth')
print(f"Dimensione: {model.size_mb} MB")

# Quantization statica
model_q8 = torch.quantization.quantize_dynamic(
    model,
    {torch.nn.Linear},
    dtype=torch.qint8
)
print(f"Dimensione Q8: {model_q8.size_mb} MB")

# Test performance
with torch.no_grad():
    output_orig = model(input_data)
    output_q8 = model_q8(input_data)

accuracy_loss = compare_outputs(output_orig, output_q8)
print(f"Perdita accuratezza: {accuracy_loss}%")

```

### Con Hugging Face

```python
from transformers import AutoModelForCausalLM
import torch

# Carica modello con quantization
model = AutoModelForCausalLM.from_pretrained(
    "microsoft/DialoGPT-medium",
    torch_dtype=torch.float16,  # Half precision
    device_map="auto",
    load_in_8bit=True  # Automatic 8-bit quantization
)

print(f"Memory footprint: {model.get_memory_footprint() / 1e9:.2f} GB")

```

## Strategie Avanzate

### Mixed Precision

```
Strategia: Diversa precisione per layer diversi

Attention layers: Q8 (più importanti)
Feed-forward: Q4 (meno critici)
Output layer: float16 (massima precisione)

Risultato: Bilanciamento ottimale qualità/dimensione

```

### Gradient Quantization

```
Durante training quantizza anche gradienti:
- Gradienti grandi: Mantenere precisione
- Gradienti piccoli: Quantizzare aggressivamente
- Comunicazione distribuita: Trasmetti meno dati

```

## Vantaggi Concreti

### Costi Infrastructure

```
Scenario: API service, 1000 richieste/ora

Modello float32:
- Server: 4x A100 (40GB VRAM each)
- Costo mensile: $12,000

Modello Q8:
- Server: 1x A100 (40GB VRAM)
- Costo mensile: $3,000
- Risparmio: 75% ($9,000/mese)

```

### Energy Consumption

```
Data center deployment:
- float32: 400W per inferenza
- Q8: 120W per inferenza
- Q4: 60W per inferenza

Per 1M inferenze/giorno:
- Risparmio energetico Q8: 70%
- Risparmio CO2: ~150 kg/giorno

```

## Limitazioni e Trade-offs

### Accuracy Degradation

```
Tasks impact diverso:

Language generation:
- Q8: Quasi impercettibile
- Q4: Leggera perdita fluency
- Q2: Significativa perdita coerenza

Mathematical reasoning:
- Q8: -5% accuracy
- Q4: -15% accuracy
- Q2: -40% accuracy

```

### Calibration Requirements

```
Per quantization ottimale serve:
1. Dataset rappresentativo per calibrazione
2. Testing accurato post-quantization
3. Fine-tuning se necessario

Senza calibrazione: Perdite performance fino al 30%

```

## Confronto Performance Reale

|Modello|Dimensione|RAM Peak|Inferenza/sec|Accuratezza|
|---|---|---|---|---|
|**LLaMA-7B float32**|28 GB|32 GB|15 tok/sec|100%|
|**LLaMA-7B Q8**|7 GB|10 GB|25 tok/sec|99.2%|
|**LLaMA-7B Q4**|3.5 GB|6 GB|40 tok/sec|96.1%|
|**LLaMA-7B Q2**|1.8 GB|4 GB|60 tok/sec|87.3%|

## Casi d'Uso Ideali

### Mobile Applications

- **Chat apps**: Q8 per qualità, Q4 per velocità
- **Voice assistants**: Q4 sufficiente per recognition
- **Real-time translation**: Q8 per accuratezza linguistica

### Edge Deployment

- **Security cameras**: Q4 per object detection
- **IoT devices**: Q2 per classificazione semplice
- **Autonomous vehicles**: Mixed precision per safety-critical

### Cost Optimization

- **Startup API**: Q8 per ridurre server costs
- **High-volume services**: Q4 per massima efficiency
- **Research prototyping**: Q2 per esperimenti rapidi

![[Pasted image 20250822122415.png]]

#dpKnowledge 