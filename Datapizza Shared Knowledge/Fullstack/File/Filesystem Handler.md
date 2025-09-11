Se in un progetto devi utilizzare dei documenti, questi vanno salvati da qualche parte. Una buona pratica è utilizzare un filesystem esterno.

1. Crea una cartella in locale
2. Crea un handler che si occupa di:
    - scrivere i file su `path` (assicurati che ci sia `mkdir` all'inizio)
    - leggere i file su `path`
3. Una volta che funziona tutto, modifica `path` con il percorso del filesystem esterno.

---

## Esempio in FastAPI

### Struttura del progetto

```
project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── file_handler.py
│   └── models.py
├── uploads/           # cartella locale per i file
└── requirements.txt

```

### File Handler (`app/file_handler.py`)

```python
import os
import shutil
from pathlib import Path
from typing import BinaryIO, Optional
from fastapi import UploadFile, HTTPException
import uuid

class FileHandler:
    def __init__(self, base_path: str = "./uploads"):
        self.base_path = Path(base_path)
        self._ensure_directory_exists()

    def _ensure_directory_exists(self):
        """Crea la directory se non esiste"""
        self.base_path.mkdir(parents=True, exist_ok=True)

    def save_file(self, file: UploadFile, subfolder: str = "") -> str:
        """
        Salva un file nel filesystem e restituisce il path relativo
        """
        try:
            # Genera un nome unico per il file
            file_extension = Path(file.filename).suffix
            unique_filename = f"{uuid.uuid4()}{file_extension}"

            # Crea il percorso completo
            if subfolder:
                folder_path = self.base_path / subfolder
                folder_path.mkdir(parents=True, exist_ok=True)
                file_path = folder_path / unique_filename
            else:
                file_path = self.base_path / unique_filename

            # Salva il file
            with open(file_path, "wb") as buffer:
                shutil.copyfileobj(file.file, buffer)

            # Restituisce il path relativo
            return str(file_path.relative_to(self.base_path))

        except Exception as e:
            raise HTTPException(status_code=500, detail=f"Errore nel salvare il file: {str(e)}")

    def read_file(self, file_path: str) -> bytes:
        """
        Legge un file dal filesystem
        """
        full_path = self.base_path / file_path

        if not full_path.exists():
            raise HTTPException(status_code=404, detail="File non trovato")

        try:
            with open(full_path, "rb") as file:
                return file.read()
        except Exception as e:
            raise HTTPException(status_code=500, detail=f"Errore nella lettura del file: {str(e)}")

    def delete_file(self, file_path: str) -> bool:
        """
        Elimina un file dal filesystem
        """
        full_path = self.base_path / file_path

        if not full_path.exists():
            return False

        try:
            full_path.unlink()
            return True
        except Exception as e:
            raise HTTPException(status_code=500, detail=f"Errore nell'eliminazione del file: {str(e)}")

    def get_file_path(self, file_path: str) -> Path:
        """
        Restituisce il percorso completo del file
        """
        return self.base_path / file_path

```

### Modelli Pydantic (`app/models.py`)

```python
from pydantic import BaseModel
from typing import Optional

class DocumentResponse(BaseModel):
    id: int
    title: str
    file_path: str
    file_size: int
    created_at: str

```

### FastAPI App (`app/main.py`)

```python
from fastapi import FastAPI, UploadFile, File, Depends, HTTPException
from fastapi.responses import FileResponse
from typing import List
import os
from datetime import datetime

from .file_handler import FileHandler
from .models import DocumentResponse

app = FastAPI(title="Document Management API")

# Inizializza il file handler
file_handler = FileHandler(base_path="./uploads")

# Database simulato (in produzione usa un vero database)
documents_db = []
document_id_counter = 1

@app.post("/documents/upload", response_model=DocumentResponse)
async def upload_document(
    title: str,
    file: UploadFile = File(...)
):
    """
    Upload di un documento
    """
    global document_id_counter

    # Salva il file nel filesystem
    file_path = file_handler.save_file(file, subfolder="documents")

    # Ottieni informazioni sul file
    full_path = file_handler.get_file_path(file_path)
    file_size = full_path.stat().st_size

    # Crea il record del documento
    document = {
        "id": document_id_counter,
        "title": title,
        "file_path": file_path,
        "file_size": file_size,
        "created_at": datetime.now().isoformat(),
        "original_filename": file.filename
    }

    documents_db.append(document)
    document_id_counter += 1

    return DocumentResponse(**document)

@app.get("/documents", response_model=List[DocumentResponse])
async def get_documents():
    """
    Recupera tutti i documenti
    """
    return [DocumentResponse(**doc) for doc in documents_db]

@app.get("/documents/{document_id}/download")
async def download_document(document_id: int):
    """
    Download di un documento
    """
    # Trova il documento nel database
    document = next((doc for doc in documents_db if doc["id"] == document_id), None)

    if not document:
        raise HTTPException(status_code=404, detail="Documento non trovato")

    # Ottieni il percorso completo del file
    file_path = file_handler.get_file_path(document["file_path"])

    if not file_path.exists():
        raise HTTPException(status_code=404, detail="File non trovato nel filesystem")

    return FileResponse(
        path=str(file_path),
        filename=document["original_filename"],
        media_type='application/octet-stream'
    )

@app.delete("/documents/{document_id}")
async def delete_document(document_id: int):
    """
    Elimina un documento
    """
    global documents_db

    # Trova il documento
    document = next((doc for doc in documents_db if doc["id"] == document_id), None)

    if not document:
        raise HTTPException(status_code=404, detail="Documento non trovato")

    # Elimina il file dal filesystem
    file_handler.delete_file(document["file_path"])

    # Rimuovi dal database
    documents_db = [doc for doc in documents_db if doc["id"] != document_id]

    return {"message": "Documento eliminato con successo"}

@app.get("/documents/{document_id}/process")
async def process_document(document_id: int):
    """
    Esempio di utilizzo del file nella logica di backend
    """
    # Trova il documento
    document = next((doc for doc in documents_db if doc["id"] == document_id), None)

    if not document:
        raise HTTPException(status_code=404, detail="Documento non trovato")

    # Leggi il contenuto del file per processarlo
    file_content = file_handler.read_file(document["file_path"])

    # Esempio di elaborazione (conta i byte)
    processed_data = {
        "document_id": document_id,
        "file_size": len(file_content),
        "processing_result": f"Processati {len(file_content)} bytes",
        "status": "completed"
    }

    return processed_data

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)

```

### Configurazione per filesystem esterno

Per utilizzare un filesystem esterno (es. AWS S3, Google Cloud Storage), modifica solo il `FileHandler`:

```python
# Per filesystem esterno
file_handler = FileHandler(base_path="/mnt/external-storage/uploads")

# O per cloud storage, implementa un CloudFileHandler che eredita da FileHandler

```

#dpKnowledge 