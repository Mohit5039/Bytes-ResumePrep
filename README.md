Resume-Aware RAG Chatbot

One-line:
A private, resume-first RAG chatbot that answers interview questions, generates STAR responses, and matches a resume to job descriptions — with strict no-hallucination behavior and clear provenance.

1 — Problem

Candidates need on-demand, factual interview coaching and resume tailoring.
Generic LLMs hallucinate.
This tool answers only from a candidate’s uploaded documents (resume, job descriptions, interview tips).

2 — What it does (Core Features)

Resume-based Q&A (facts pulled from resume)

STAR answer generator using resume facts

Resume vs Job-Description matching (matches & gaps)

Resume bullet rewriting for target role

Provenance: shows which document/chunk produced the answer

Fallback: I don't know based on the provided documents. for OOS queries


3 — Architecture
+-------------------+         +------------------+         +---------------------------+
|     Documents     |  --->   |   Preprocessor   |  --->   | Embeddings + FAISS Index |
| (resume, jobdesc, |         |   (chunking)     |         |  (sentence-transformers)  |
|   tips)           |         +------------------+         +------------+--------------+
                                                                     |
                                                                     v
                                                            +--------------------+
                                                            |   Retriever        |
                                                            |   (FAISS top-k)    |
                                                            +---------+----------+
                                                                      |
                                                                      v
                                                         +--------------------------+
                                                         |  LLM (HF Inference or   |
                                                         |  local GGUF model)      |
                                                         +------------+-------------+
                                                                      |
                                                                      v
                                                           +----------------------+
                                                           |   Frontend (UI)      |
                                                           |  Streamlit Chat UI   |
                                                           |  Modes + Provenance  |
                                                           +----------------------+
4 — Design & Approach (Short)

Ingest: Place resume.txt, jobdesc.txt, tips.txt in docs/.

Chunk: Split docs (~300 words, 100 overlap) → chunks.jsonl.

Embed & Index: sentence-transformers → FAISS (IndexFlatIP, normalized vectors).

Query: Embed query → FAISS search → if max similarity < threshold (e.g., 0.15) → fallback.

Generate: Build strict prompt (ONLY use context) → call LLM → return answer + sources.

UI: Streamlit chat → shows answer, confidence, and expandable chunk sources.

5 — Tech Stack
Backend / RAG

Python

sentence-transformers (embeddings)

FAISS (vector search)

PyPDF2 (for document parsing)

LLM options:

Hugging Face Inference API (simple, no local model install)

llama-cpp-python (local GGUF model if offline)

Frontend

Streamlit

Chat UI (st.chat_message)

Mode selector (st.selectbox)

Expandable sources (st.expander)

Confidence indicator

Deployment

Local demo (recommended for hackathon)

Optional:

Docker → Azure App Service / Container Apps

Streamlit Cloud (if using HF Inference, not local models)