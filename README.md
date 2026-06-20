# 🏥 Medical RAG Assistant

A Retrieval-Augmented Generation (RAG) system that lets you ask natural-language questions about medical research papers and get evidence-based, source-cited answers — instead of manually searching through pages of dense literature.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Devamalyaa/medical-rag-assistant/blob/main/medical_rag_assistant.ipynb)

> Replace `Devamalyaa/medical-rag-assistant` above with your actual GitHub path once this repo is live, so the badge links to your real notebook.

---

## 📖 Overview

Medical research papers are long, dense, and hard to search by keyword alone. This project builds a RAG pipeline that:

1. Loads medical PDFs and splits them into semantically meaningful chunks
2. Embeds each chunk into a vector space using a sentence-transformer model
3. Indexes those vectors in FAISS for fast similarity search
4. Retrieves the most relevant chunks for a user's question
5. Passes that context to Google Gemini 2.5 Flash, which generates a grounded answer **with page citations** — and explicitly says so when the answer isn't in the source material, rather than guessing

## ⚙️ Architecture

```
PDF Papers
   │
   ▼
PyPDFLoader  →  RecursiveCharacterTextSplitter (1000 chars, 200 overlap)
   │
   ▼
Sentence-Transformers (all-MiniLM-L6-v2, 384-dim, cosine similarity)
   │
   ▼
FAISS Vector Store
   │
   ▼
Similarity Search (top-k retrieval)  →  Context-constrained prompt  →  Gemini 2.5 Flash
   │
   ▼
Answer + Cited Source Pages
```

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| Orchestration | LangChain |
| PDF Parsing | PyPDF |
| Embeddings | Sentence-Transformers (`all-MiniLM-L6-v2`) |
| Vector Store | FAISS |
| LLM | Google Gemini 2.5 Flash |
| Runtime | Google Colab |

## ✨ Key Features

- **Semantic chunking** — splits documents on paragraph/sentence boundaries (not arbitrary character cuts) for more coherent retrieval
- **Source-grounded answers** — every response cites the exact page(s) it pulled from
- **Hallucination guardrails** — the prompt explicitly instructs the model to say "not enough information" rather than fabricate an answer when a question falls outside the indexed papers
- **Multi-document support** — knowledge base can be expanded with additional papers; tested with a 2-paper, 48-chunk combined index
- **Interactive Q&A mode** — a simple CLI-style loop for live querying inside the notebook

## 📂 Project Structure

```
medical-rag-assistant/
├── medical_rag_assistant.ipynb  
├── requirements.txt              
├── README.md
├── assets/                        
└── data/                          
```

## 🚀 Setup & Usage

This notebook is built for **Google Colab**.

1. **Open the notebook** — click the "Open in Colab" badge above, or upload `medical_rag_assistant.ipynb` to your own Colab.
2. **Add your Gemini API key** — get a free key from [Google AI Studio](https://aistudio.google.com/app/apikey), then in Colab go to the 🔑 Secrets panel (left sidebar) and add it as `GEMINI_API_KEY`. Enable notebook access for the secret.
3. **Mount Google Drive** and create the folder `MyDrive/medical-rag/data/`.
4. **Add your PDFs** — drop one or more medical research papers into that folder as `paper1.pdf`, `paper2.pdf`, etc. (Use your own papers, or a public domain / open-access source such as PubMed Central, since copyrighted papers shouldn't be redistributed.)
5. **Run all cells** (Runtime → Run all). The first run downloads the embedding model (~80 MB), which takes a minute.
6. **Ask questions** — either via the example queries in the test cells, or the interactive prompt at the end of the notebook.

## 💬 Example Queries

The notebook was tested against questions such as:

- *"What are the main symptoms of [condition] syndrome?"*
- *"What treatment options are discussed in the papers?"*
- *"How many patients were studied in this research?"*
- An intentionally out-of-scope question (e.g. about an unrelated physics topic), to verify the system correctly declines rather than fabricating an answer

See `/assets` for screenshots of real Q&A output from the notebook.

## 📊 Results

- Indexed **2 medical research papers → 48 semantic chunks** (single-paper run: ~30 chunks, ~909 characters average chunk size)
- 384-dimensional embeddings via `all-MiniLM-L6-v2`, normalized for cosine similarity search
- Correctly retrieves and cites source pages for in-scope questions; correctly identifies and flags out-of-scope questions instead of hallucinating

## 🔭 Limitations & Future Improvements

- Currently tested on a small (2-paper) corpus — scaling to a larger knowledge base would benefit from a more persistent vector store (e.g. Chroma or a hosted FAISS index) rather than rebuilding in-memory each run
- No automated evaluation of answer faithfulness yet (e.g. RAGAS) — would strengthen confidence in answer quality at scale
- Currently Colab-only; could be refactored into a standalone Python package / Streamlit app for easier non-technical use

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Devamalyaa G R**
[LinkedIn](https://www.linkedin.com/in/g-r-devamalyaa-81a78a31a) · [GitHub](https://github.com/Devamalyaa)
