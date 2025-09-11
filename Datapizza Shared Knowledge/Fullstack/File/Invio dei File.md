Abbiamo due direzioni possibili:

# Frontend → Backend

Per caricare file dal frontend al backend posso procedere come segue:

```python
# backend/app/file_handler.py
from fastapi import UploadFile, File
from typing import List
import uuid
import os
import shutil
from pathlib import Path

@router.post("/session/{session_id}/documents/upload")
async def upload_documents(
    session_id: int,
    files: List[UploadFile] = File(...),
    current_user: User = Depends(get_current_user),
) -> dict:
    ...

```

```jsx
// frontend/src/components/FileUploadComponent.tsx

/**
 * Uploads files to a specific session
 * @param sessionId - The ID of the session
 * @param files - Array of File objects to upload
 * @param onProgress - Optional callback for upload progress
 * @returns Promise with upload results
 */
export const uploadDocuments = async (
    sessionId: string,
    files: File[],
    onProgress?: (progress: number) => void
) => {
    // Validate files before upload
    const MAX_FILE_SIZE = 50 * 1024 * 1024; // 50MB
    const ALLOWED_TYPES = [
        'application/pdf',
        'text/plain',
        'application/msword',
        'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
        'text/markdown'
    ];

    const validFiles = [];
    const invalidFiles = [];

    for (const file of files) {
        if (file.size > MAX_FILE_SIZE) {
            invalidFiles.push({
                file: file.name,
                error: `File size (${(file.size / 1024 / 1024).toFixed(2)}MB) exceeds 50MB limit`
            });
        } else if (!ALLOWED_TYPES.includes(file.type) && !file.name.toLowerCase().endsWith('.md')) {
            invalidFiles.push({
                file: file.name,
                error: `File type ${file.type} not allowed`
            });
        } else {
            validFiles.push(file);
        }
    }

    if (validFiles.length === 0) {
        throw new Error('No valid files to upload');
    }

    // Create FormData for multipart upload
    const formData = new FormData();
    validFiles.forEach(file => {
        formData.append('files', file);
    });

    // Upload with progress tracking
    const uploadUrl = `${API_BASE_URL}/session/${sessionId}/documents/upload`;

    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();

        // Track upload progress
        if (onProgress) {
            xhr.upload.addEventListener('progress', (event) => {
                if (event.lengthComputable) {
                    const progress = (event.loaded / event.total) * 100;
                    onProgress(progress);
                }
            });
        }

        xhr.addEventListener('load', () => {
            if (xhr.status >= 200 && xhr.status < 300) {
                try {
                    const result = JSON.parse(xhr.responseText);
                    resolve({
                        ...result,
                        invalidFiles
                    });
                } catch (error) {
                    reject(new Error('Invalid response format'));
                }
            } else {
                reject(new Error(`Upload failed with status ${xhr.status}`));
            }
        });

        xhr.addEventListener('error', () => {
            reject(new Error('Network error during upload'));
        });

        xhr.open('POST', uploadUrl);
        xhr.setRequestHeader('Accept', 'application/json');
        xhr.withCredentials = true; // Include cookies for authentication

        xhr.send(formData);
    });
};

```

```jsx
// frontend/src/components/FileUploadComponent.tsx
export const FileUploadComponent = ({ sessionId, onUploadComplete }) => {
    const [isDragging, setIsDragging] = useState(false);
    const [uploadProgress, setUploadProgress] = useState(0);
    const [isUploading, setIsUploading] = useState(false);
    const fileInputRef = useRef(null);

    const handleFileUpload = async (files) => {
        if (!files || files.length === 0) return;

        setIsUploading(true);
        setUploadProgress(0);

        try {
            const result = await uploadDocuments(
                sessionId,
                Array.from(files),
                setUploadProgress
            );

            onUploadComplete?.(result);

            if (result.successful_uploads > 0) {
                console.log(`Successfully uploaded ${result.successful_uploads} file(s)`);
            }

        } catch (error) {
            console.error('Upload error:', error);
        } finally {
            setIsUploading(false);
            setUploadProgress(0);
        }
    };

    const handleDragOver = (e) => {
        e.preventDefault();
        setIsDragging(true);
    };

    const handleDragLeave = (e) => {
        e.preventDefault();
        setIsDragging(false);
    };

    const handleDrop = (e) => {
        e.preventDefault();
        setIsDragging(false);
        handleFileUpload(e.dataTransfer.files);
    };

    const handleFileSelect = (e) => {
        handleFileUpload(e.target.files);
        e.target.value = ''; // Reset for re-selection
    };

    return (
        <div className="file-upload-container">
            <div
                className={`upload-zone ${isDragging ? 'dragging' : ''} ${isUploading ? 'uploading' : ''}`}
                onDragOver={handleDragOver}
                onDragLeave={handleDragLeave}
                onDrop={handleDrop}
                onClick={() => fileInputRef.current?.click()}
            >
                <input
                    type="file"
                    ref={fileInputRef}
                    onChange={handleFileSelect}
                    multiple
                    accept=".pdf,.txt,.doc,.docx,.md"
                    style={{ display: 'none' }}
                />

                {isUploading ? (
                    <div className="upload-progress">
                        <div className="progress-bar">
                            <div
                                className="progress-fill"
                                style={{ width: `${uploadProgress}%` }}
                            />
                        </div>
                        <p>Uploading... {Math.round(uploadProgress)}%</p>
                    </div>
                ) : (
                    <div className="upload-prompt">
                        <p>📁 Drag & drop files here or click to select</p>
                        <small>Supported: PDF, TXT, DOC, DOCX, MD (max 50MB each)</small>
                    </div>
                )}
            </div>
        </div>
    );
};

```

# Backend → Frontend

Se voglio passare dei documenti (pdf) da backend a frontend posso procedere come segue:

1. A be creo una rotta per ritornare un documento binario:

```python
@router.get("/session/{session_id}/document/{document_id}/pdf")
    async def get_document_pdf(
        session_id: int,
        document_id: str,
        current_user: User = Depends(get_current_user),
    ) -> Response:
        """
        Retrieve a PDF document from a specific session.

        Flow:
        1. Check if the session exists and belongs to the user
        2. Check if the document exists in this session
        3. Get the file path using FileHandler
        4. Verify the file exists on filesystem
        5. Verify it's a PDF file
        6. Read the entire file and send it back as binary so that the frontend can cache it as a Blob.

        Args:
            session_id: The ID of the session containing the document
            document_id: The filename of the document to retrieve
            current_user: The authenticated user

        Returns:
            FileResponse: The PDF file

        Raises:
            HTTPException: If session not found, document not found, or access denied
        """
        try:
            # Verify that the session exists and belongs to the user
            check_session = await asyncio.to_thread(
                dependencies.crud.get_check_session, current_user.id, session_id
            )

            # Verify that the document exists in this session
            if document_id not in check_session.document_ids:
                raise HTTPException(
                    status_code=404,
                    detail=f"Document '{document_id}' not found in session {session_id}",
                )

            # Get the file path using FileHandler
            file_handler = FileHandler(str(session_id))
            file_path = os.path.join(file_handler.session_path, document_id)

            # Verify the file exists on filesystem
            if not os.path.exists(file_path):
                raise HTTPException(
                    status_code=404,
                    detail=f"File '{document_id}' not found on filesystem",
                )

            # Verify it's a PDF file
            if not document_id.lower().endswith(".pdf"):
                raise HTTPException(
                    status_code=400,
                    detail=f"Document '{document_id}' is not a PDF file",
                )

            # Read the entire file and send it back as binary so that the frontend can cache it as a Blob.
            async with aiofiles.open(file_path, "rb") as f:  # type: ignore[call-arg]
                file_bytes = await f.read()

            return Response(
                content=file_bytes,
                media_type="application/pdf",
                headers={
                    "Content-Disposition": f'inline; filename="{document_id}"',
                    "Cache-Control": "no-cache",
                    "X-Content-Type-Options": "nosniff",
                    "Accept-Ranges": "bytes",
                },
            )

        except ResourceNotFoundException as exc:
            raise HTTPException(status_code=404, detail=str(exc))
        except DatabaseException as exc:
            raise HTTPException(
                status_code=503, detail=f"Database unavailable: {str(exc)}"
            )
        except Exception as exc:
            raise HTTPException(status_code=500, detail=str(exc))

```

1. A Frontend hitto la rotta quando clicco su una source e fetcho il contenuto all'interno di un blob (che fa da cache). Una volta che ho cachato in un blob con chiave `session_id:document_id` ogni volta che visualizzo il documento di quella sessione riutilizzo quel blob (ottimo per scalabilità)

```jsx
/**
 * Opens a PDF document at a specific page in a new tab for viewing (not download)
 * @param sessionId - The ID of the session
 * @param documentId - The ID of the document
 * @param pageNumber - The page number to navigate to (optional)
 */
export const openPdfDocument = async (
    sessionId: string,
    documentId: string,
    pageNumber?: number
) => {

    const cacheKey = `${sessionId}-${documentId}`;
    let blobUrl = pdfBlobUrlCache.get(cacheKey);

    // Se non esiste nella cache, effettua la chiamata e crea il blob una sola volta
    if (!blobUrl) {
        // ottieni la rotta, genera l'url da hittare
        const pdfUrl = getPdfUrl(sessionId, documentId);

        // hitta la rotta, ottieni il file byte
        const response = await fetch(pdfUrl, {
            credentials: "include",
            headers: {
                "Accept": "application/pdf",
            },
        });

        if (!response.ok) {
            throw new Error(`Impossibile recuperare il PDF (status ${response.status})`);
        }
        // salva in blob
        const blob = await response.blob();
        blobUrl = URL.createObjectURL(blob);
        pdfBlobUrlCache.set(cacheKey, blobUrl);
    }

    // visualizza nel browser alla pagina specificata
    const viewUrl = pageNumber ? `${blobUrl}#page=${pageNumber}` : blobUrl;
    window.open(viewUrl, "_blank", "noopener,noreferrer");

};

```

1. Quando mi sposto di sessione (e quindi non mi serve più quel blob) elimino il blob temporaneo

```jsx
export const clearCachedPdfUrl = (
    sessionId?: string,
    documentId?: string,
) => {
    if (sessionId && documentId) {
        const key = `${sessionId}-${documentId}`;
        const url = pdfBlobUrlCache.get(key);
        if (url) {
            URL.revokeObjectURL(url);
            pdfBlobUrlCache.delete(key);
        }
    } else {
        // Clear everything
        pdfBlobUrlCache.forEach((url) => URL.revokeObjectURL(url));
        pdfBlobUrlCache.clear();
    }
};

```

#dpKnowledge 