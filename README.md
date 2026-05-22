# Your-OWN-AI 🧠

A fully local **Vector Database and RAG Pipeline** built from scratch in C++ with an interactive web UI.  
This project empowers you to run your own AI environment locally. It implements **HNSW**, **KD-Tree**, and **Brute Force** search algorithms side-by-side, combined with a **RAG (Retrieval-Augmented Generation) pipeline** powered by a local LLM via Ollama.

> **Note:** This project was cloned and adapted to serve as a foundation for building a personalized, privacy-first local AI platform. It demystifies how production vector databases (like Pinecone, Weaviate, or Chroma) and AI assistants actually work under the hood.

---

## 🚀 What This Project Does

| Feature | Description |
|---|---|
| **3 Search Algorithms** | HNSW (production-grade), KD-Tree, Brute Force — run all three and compare speed |
| **3 Distance Metrics** | Cosine similarity, Euclidean distance, Manhattan distance |
| **Real Document Embedding** | Paste any text → Ollama embeds it locally with `nomic-embed-text` (768D) |
| **RAG Pipeline** | Ask questions about your documents → HNSW retrieves context → local LLM answers |
| **Live Visualization** | 2D PCA Scatter Plot of your semantic space — watch clusters form |
| **Full REST API** | CRUD endpoints: insert, delete, search, benchmark, hnsw-info |

---

## 🧠 How It Works Behind the Scenes

```text
Your Text / Documents
    │
    ▼
Ollama (nomic-embed-text)          ← converts text to a 768-dimensional vector locally
    │
    ▼
HNSW Index (C++)                   ← indexes the vector in a multilayer graph
    │
    ▼
Semantic Search                    ← finds nearest neighbors in vector space for your queries
    │
    ▼
Ollama (llama3.2)                  ← reads retrieved document chunks, generates an answer
    │
    ▼
Answer
```

**HNSW (Hierarchical Navigable Small World)** is the industry-standard algorithm. It builds a multilayer graph where searches start at a sparse top layer and zoom in, achieving O(log N) complexity.

---

## 🛠️ Prerequisites

You need **3 things** installed on your Windows machine to run Your-OWN-AI:

1. **MSYS2** (provides the `g++` compiler for C++)
2. **Git**
3. **Ollama** (runs the local AI models locally)

---

## ⚙️ Step-by-Step Setup (Windows)

### Step 1 — Install MSYS2 (C++ Compiler)

1. Go to **https://www.msys2.org** and download the installer.
2. Run the installer, keep default path (`C:\msys64`).
3. After install, open **MSYS2 UCRT64** from Start Menu.
4. Run these commands:

```bash
pacman -Syu
```
*(Close and reopen the terminal if it asks you to)*

```bash
pacman -S mingw-w64-ucrt-x86_64-gcc
```

5. Add g++ to your Windows PATH:
   - Press `Win + R`, type `sysdm.cpl`, press Enter
   - Click **Advanced** → **Environment Variables**
   - Under **System variables**, find **Path**, click **Edit**
   - Click **New** and add: `C:\msys64\ucrt64\bin`
   - **Open a new PowerShell** and verify: `g++ --version`

### Step 2 — Install Git

1. Go to **https://git-scm.com/download/win** and download Git for Windows.
2. Run the installer with default settings.
3. Verify in PowerShell: `git --version`

### Step 3 — Install Ollama (Local AI Models)

1. Go to **https://ollama.com** and download for Windows.
2. Run the installer. Ollama will start in the system tray.
3. Open **PowerShell** and pull the required models:

```powershell
ollama pull nomic-embed-text
```
*(Embedding model)*

```powershell
ollama pull llama3.2
```
*(Language model. Note: You need ~8GB RAM. For lower specs, use `llama3.2:1b`)*

### Step 4 — Clone & Compile

Open **PowerShell** and run:

```powershell
# Clone the repository (adjust the URL if you have pushed it to your own GitHub)
git clone <your-repository-url>
cd Your-OWN-AI
```

Compile the C++ Server:

```powershell
g++ -std=c++17 -O2 main.cpp -o db -lws2_32
```
This produces `db.exe`. (Takes ~10–20 seconds).

---

## ▶️ Running Your-OWN-AI

**Terminal 1** — Start Ollama (if not already running in the background):
```powershell
ollama serve
```

**Terminal 2** — Start the C++ Backend:
```powershell
./db
```

**Open your browser** and go to the address printed in the terminal (e.g., `http://localhost:8080` or `http://localhost:9090`). Note: If the frontend doesn't connect, ensure `index.html` `API` constant matches the port the server listens on.

---

## 💡 Using the Web Interface

### 1. Search Tab
- Use the provided demo vectors to visualize how semantic search works.
- Compare **HNSW**, **KD-Tree**, and **Brute Force** performance in real-time.
- View the 2D PCA projection of vectors.

### 2. Documents Tab (Real Embeddings)
- Type a title and paste any text (notes, articles, code).
- Click **EMBED & INSERT**. Your-OWN-AI automatically chunks the text, calls Ollama to embed it, and indexes it via HNSW.

### 3. Ask AI Tab (RAG)
- Ask questions based on the documents you inserted.
- The pipeline retrieves the top-K relevant chunks from the C++ Vector DB and streams a contextualized answer from the local LLM.

---

## 🔧 Troubleshooting

| Problem | Fix |
|---|---|
| `Ollama: OFFLINE` | Run `ollama serve` in a terminal |
| `g++: command not found` | Ensure `C:\msys64\ucrt64\bin` is in your Windows PATH |
| `undefined reference to WSA...` | Add `-lws2_32` to your g++ command during compilation |
| LLM answer is too slow | Switch to the 1B model (`ollama pull llama3.2:1b`) and update `main.cpp` `genModel` variable |

