# 📄 LLM-Powered Intelligent Query–Retrieval System

## 📌 Problem Statement  
Design and implement an **LLM-powered intelligent query–retrieval system** that can process large, unstructured, and multilingual documents to answer contextual, domain-specific questions.  

This solution is targeted for **insurance, legal, HR, and compliance sectors**, where accuracy, clarity, and explainability are critical.  

---

## 🎯 Overview  
We have comprehensively addressed the problem statement by creating a system that:  

- Ingests diverse document formats: **PDF, DOCX, email, scanned images, or even live JSON/HTML data from web/API sources**.  
- Performs **robust text extraction and preprocessing**, with **OCR fallback** for image-based documents.  
- Uses **intelligent chunking** to preserve context.  
- Generates **Azure OpenAI embeddings** and stores them in a **FAISS vector database** for fast semantic search.  
- Allows **natural language questions** and returns **context-aware answers**, translated back to the question’s language if needed.  
- Provides a fully functional **REST API with Swagger UI** for easy testing and validation.  

---

## 🚀 Features  

- ✅ Multi-format ingestion (**PDF, DOCX, email, JSON, HTML**)  
- ✅ OCR fallback for scanned PDFs using **pytesseract**  
- ✅ **Multilingual** document and query support  
- ✅ Fast, **context-aware retrieval** using **FAISS**  
- ✅ Context-grounded answers with relevant clause quoting  
- ✅ **Web scraping** and API data extraction  
- ✅ Cached embeddings for improved performance on repeated queries  
- ✅ Built-in **Swagger UI** for testing  

---

## 🛠️ Tech Stack  

- **FastAPI** – REST API framework  
- **LangChain** – LLM orchestration  
- **Azure OpenAI** – LLM and embeddings  
- **FAISS** – Vector database for semantic search  
- **PyMuPDF, docx2txt, email.parser** – Document parsing  
- **pytesseract + pdf2image** – OCR for scanned PDFs  
- **BeautifulSoup** – HTML parsing  

---

## ⚙️ How It Works (Step-by-Step)  

1. Client **POSTs** to `/api/v1/hackrx/run` with:  
   - `documents` → comma-separated URLs  
   - `questions` → list of queries  

2. For each document URL:  
   - If the URL returns **HTML/JSON/plain text**, use it directly (useful for API endpoints).  
   - Otherwise:  
     - **PDFs** → PyMuPDF text extraction → fallback OCR (pdf2image + pytesseract)  
     - **DOCX** → docx2txt  
     - **EML (emails)** → email.parser.BytesParser  

3. Extracted text is **normalized** (remove hyphen-newlines, collapse whitespace).  

4. If document language ≠ English → LLM translates content into **English for indexing**.  

5. Text is **chunked** (`chunk_size=1200`, `chunk_overlap=200`) and embedded using **Azure OpenAI embeddings**.  

6. A **RetrievalQA chain** is created using:  
   - FAISS retriever  
   - Conservative `PromptTemplate` (GENERIC_PROMPT) → instructs LLM to **answer only from context** and avoid hallucinations.  

7. For each question:  
   - Runs either a **specialized flow** (e.g., token/flight lookups)  
   - Or sends query → RetrievalQA chain → final answer.  

---

## 💾 Caching & Persistence  

- Uses **FAISS for persistent local storage**.  
- **Cache mechanism** prevents redundant embedding generation for previously processed documents.  

---

## 🧠 Prompting & Explainability  

- Carefully crafted prompts to extract **relevant, context-aware answers**.  
- Debug logs explain processing steps for transparency.  

---

## ⚠️ Troubleshooting & Known Issues  

- Very **large documents** may require additional chunking.  
- **Network timeouts** can occur if URLs are slow.  
- Ensure correct **permissions for persistent storage directory**.  

---

## 🧪 Example: Sample Policy Test (Knee Surgery)  

- Upload **medical policy documents**.  
- Query:  
  ```text
  Is knee surgery covered under policy X?
