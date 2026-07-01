# 📚 ML Research Paper Chatbot

A Retrieval-Augmented Generation (RAG) chatbot that lets you ask questions across 12 landmark machine learning research papers in plain English — with every answer cited by source paper and page number.

Built as a portfolio project by a final-year Data Science undergraduate at SLIIT, Sri Lanka.

---

## 🎯 What It Does

- Indexes 12 foundational ML papers from ArXiv as a local vector database
- Accepts natural language questions like *"How does BERT differ from GPT?"*
- Retrieves the most relevant chunks from the papers using semantic search
- Generates plain-English answers using Gemini 2.5 Flash
- Cites every claim with the source paper and page number
- Provides a clean chat interface built with Gradio

---

## 📄 Indexed Papers

| # | Paper | Authors | Year |
|---|-------|---------|------|
| 1 | Attention Is All You Need (Transformer) | Vaswani et al. | 2017 |
| 2 | BERT | Devlin et al. | 2018 |
| 3 | Deep Residual Learning (ResNet) | He et al. | 2015 |
| 4 | Generative Adversarial Networks (GAN) | Goodfellow et al. | 2014 |
| 5 | An Image is Worth 16×16 Words (ViT) | Dosovitskiy et al. | 2020 |
| 6 | Retrieval-Augmented Generation (RAG) | Lewis et al. | 2020 |
| 7 | Efficient Estimation of Word Representations (Word2Vec) | Mikolov et al. | 2013 |
| 8 | Adam: A Method for Stochastic Optimization | Kingma & Ba | 2014 |
| 9 | Batch Normalization | Ioffe & Szegedy | 2015 |
| 10 | Dropout | Srivastava et al. | 2014 |
| 11 | Human-level Control through Deep RL (Atari) | Mnih et al. | 2015 |
| 12 | Scaling Laws for Neural Language Models | Kaplan et al. | 2020 |

---

## 🏗️ Architecture

```
12 ArXiv PDFs
      ↓  PyMuPDF extracts text page by page
Raw text per paper
      ↓  LangChain RecursiveCharacterTextSplitter (chunk_size=1000, overlap=200)
826 text chunks
      ↓  Gemini embedding model (gemini-embedding-001)
826 vectors + metadata
      ↓  ChromaDB persisted locally
Vector store on disk
      ↓  At query time: embed question → retrieve top 5 chunks → fill prompt → Gemini 2.5 Flash
Cited answer in plain English
```

---

## 🛠️ Tech Stack

| Component | Tool |
|---|---|
| Framework | LangChain |
| Vector Database | ChromaDB (local, persistent) |
| Embeddings | Google Gemini (`gemini-embedding-001`) |
| LLM | Google Gemini 2.5 Flash |
| UI | Gradio |
| PDF Parsing | PyMuPDF (fitz) |
| Paper Source | ArXiv (free, public) |
| Language | Python 3.13 |

---

## 🚀 How to Run Locally

### 1. Clone the repository
```bash
git clone https://github.com/VDewMin/ml-research-chatbot.git
cd ml-research-chatbot
```

### 2. Create and activate virtual environment
```bash
python -m venv .venv --system-site-packages
.venv\Scripts\python.exe -m pip install -r requirements.txt
```

### 3. Set up your API key
Create a `.env` file in the project root:
```
GOOGLE_API_KEY=your_gemini_api_key_here
```
Get a free API key at [Google AI Studio](https://aistudio.google.com).

### 4. Download the papers
Run all cells in `notebooks/01_download_papers.ipynb`

### 5. Build the vector store (one-time, ~15 minutes)
Run all cells in `notebooks/02_chunk_and_embed.ipynb`

> This embeds 826 chunks using the Gemini free tier (rate-limit-aware batching built in)

### 6. Launch the chatbot
Run all cells in `notebooks/03_query_and_ui.ipynb`

A local URL will appear — open it in your browser.

---

## 💡 Example Questions to Try

- *What problem does the Transformer architecture solve?*
- *How does BERT differ from GPT-2?*
- *What is a residual connection and why does it help training?*
- *How do GANs work — what are the generator and discriminator doing?*
- *What is the RAG architecture and why is it better than fine-tuning?*
- *Why does the Adam optimizer work better than vanilla SGD?*
- *What did the Atari paper prove about reinforcement learning?*
- *What do scaling laws say about model performance?*

---

## 📁 Project Structure

```
ml-research-chatbot/
│
├── papers/                          # ArXiv PDFs (downloaded by notebook 01)
├── chroma_db/                       # ChromaDB vector store (created by notebook 02)
│
├── notebooks/
│   ├── 01_download_papers.ipynb     # Download 12 ArXiv PDFs
│   ├── 02_chunk_and_embed.ipynb     # Chunk, embed, and store in ChromaDB
│   └── 03_query_and_ui.ipynb        # RAG chain + Gradio chat UI
│
├── .env                             # Your API key (not pushed to GitHub)
├── .gitignore                       # Excludes .env, chroma_db/, papers/, .venv/
└── requirements.txt                 # All Python dependencies
```

---

## 🔑 Key Design Decisions

**Why RAG instead of fine-tuning?**
Fine-tuning would require retraining a large model every time a new paper is added. RAG lets us add papers by simply re-running the indexing notebook — no model training needed.

**Why ChromaDB?**
ChromaDB persists to disk with a single `persist_directory` argument. Index once, query forever — no re-embedding on restart.

**Why chunk with overlap?**
A chunk_overlap of 200 characters ensures that sentences split across chunk boundaries aren't lost from either chunk, preserving context at the edges.

**Why rate-limit-aware batching?**
The Gemini free tier allows 100 embedding requests per minute. The indexing notebook batches 80 chunks at a time with 65-second pauses — staying safely within limits without requiring a paid plan.

---

## 👩‍💻 Author

**W. A. Vinidya Dewmin**
Third-year BSc (Hons) Information Technology — Data Science specialisation
SLIIT, Sri Lanka
[GitHub](https://github.com/VDewMin)