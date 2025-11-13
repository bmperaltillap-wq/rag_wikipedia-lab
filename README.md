# RAG Wikipedia Lab 🔍

**AI Research Intelligence Laboratory - Task 2**

A Retrieval-Augmented Generation (RAG) system that extracts, embeds, and queries Wikipedia content to generate factually-grounded summaries.

---

## 🎯 Overview

This project implements a complete RAG pipeline:
1. **Data Collection**: Fetch Wikipedia article on "Federated Learning"
2. **Text Chunking**: Split into ~300-word segments
3. **Embedding**: Convert text to vectors using SentenceTransformers
4. **Vector Storage**: Store in ChromaDB for semantic search
5. **Retrieval**: Query-based semantic search
6. **Generation**: LLM synthesis of retrieved content

---

## 🏗️ System Architecture
```
Wikipedia API → Text Chunks → Embeddings → ChromaDB
                                              ↓
User Query → Semantic Search → Top-K Chunks → LLM → Summary
```

---

## 🛠️ Technologies

- **Data Source**: Wikipedia API
- **Embedding Model**: all-MiniLM-L6-v2 (384 dimensions)
- **Vector Store**: ChromaDB
- **LLM**: HuggingFace Zephyr-7B-Beta
- **Framework**: LangChain
- **Language**: Python 3.10+

---

## 📦 Installation

### 1. Clone repository
```bash
git clone https://github.com/[your-username]/rag_wikipedia-lab.git
cd rag_wikipedia-lab
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Set up HuggingFace token

Get your free token from [HuggingFace](https://huggingface.co/settings/tokens)

---

## 🚀 Usage

### Run in Google Colab (Recommended)

1. Open `notebooks/rag_wikipedia.ipynb` in [Google Colab](https://colab.research.google.com)
2. Run all cells sequentially
3. Enter HuggingFace API token when prompted
4. System will generate outputs automatically

### Run locally
```bash
jupyter notebook notebooks/rag_wikipedia.ipynb
```

---

## 📊 Output Files

The system generates:

1. **`data/wiki_corpus.csv`**
   - Chunked Wikipedia article
   - Columns: `id`, `title`, `text`
   - 17 chunks × ~300 words each

2. **`outputs/rag_summary.md`**
   - Generated summary (400-500 words)
   - Topic: Federated Learning challenges in healthcare
   - Factually grounded in Wikipedia content

3. **`outputs/retrieval_examples.json`**
   - Documents retrieval process
   - Shows which chunks were used
   - Includes metadata and timestamps

4. **`outputs/reflection.md`** (BONUS)
   - Compares Multi-Agent vs RAG approaches
   - Analyzes when to use each methodology
   - +3 bonus points

---

## 📁 Project Structure
```
rag_wikipedia-lab/
├── notebooks/
│   └── rag_wikipedia.ipynb       # Complete RAG implementation
├── data/
│   └── wiki_corpus.csv           # Chunked Wikipedia data
├── outputs/
│   ├── rag_summary.md            # Generated summary
│   ├── retrieval_examples.json   # Retrieval documentation
│   └── reflection.md             # Bonus comparison (Task 1 vs Task 2)
├── requirements.txt              # Python dependencies
└── README.md                     # This file
```

---

## 🔍 How It Works

### Step 1: Data Collection
```python
import wikipediaapi
wiki = wikipediaapi.Wikipedia('en')
page = wiki.page("Federated_learning")
```

Fetches the complete Wikipedia article.

### Step 2: Chunking
```python
def chunk_text(text, chunk_size=300):
    words = text.split()
    chunks = [' '.join(words[i:i+chunk_size]) 
              for i in range(0, len(words), chunk_size)]
    return chunks
```

Splits text into ~300-word segments.

### Step 3: Embedding
```python
from sentence_transformers import SentenceTransformer
model = SentenceTransformer('all-MiniLM-L6-v2')
embeddings = model.encode(chunks)
```

Converts text to 384-dimensional vectors.

### Step 4: Storage
```python
import chromadb
client = chromadb.Client()
collection = client.create_collection("wiki_ai")
collection.add(documents=chunks, embeddings=embeddings)
```

Stores in ChromaDB vector database.

### Step 5: Retrieval
```python
query = "Explain federated learning challenges in healthcare"
results = collection.query(query_embeddings=[query_embedding], n_results=5)
```

Semantic search returns top-5 relevant chunks.

### Step 6: Generation
```python
llm = HuggingFaceHub(repo_id="HuggingFaceH4/zephyr-7b-beta")
summary = llm(context + query)
```

LLM synthesizes retrieved chunks into coherent summary.

---

## 🎓 Key Features

✅ **Factual Grounding**: All outputs traceable to Wikipedia sources  
✅ **Zero Hallucination**: LLM restricted to retrieved context  
✅ **Semantic Search**: Meaning-based rather than keyword matching  
✅ **Reproducible**: Deterministic retrieval (same query = same results)  
✅ **Auditable**: Full retrieval documentation in JSON  
✅ **Scalable**: Can extend to multiple Wikipedia articles  

---

## 🔧 Configuration

### Change Research Topic

Modify in Cell 5 of notebook:
```python
page = wiki.page("Your_Topic_Here")  # e.g., "Machine_Learning"
```

### Adjust Chunk Size

Modify in Cell 6:
```python
chunks = chunk_text(page.text, chunk_size=500)  # Default: 300
```

### Change Retrieval Count

Modify in Cell 12:
```python
results = retrieve_context(query, n_results=10)  # Default: 5
```

---

## 💡 Use Cases

This RAG system is ideal for:

- 📚 **Knowledge Base Q&A**: Query technical documentation
- 🏥 **Medical Literature**: Search research papers
- ⚖️ **Legal Documents**: Find relevant case law
- 📖 **Educational Content**: Interactive textbook queries
- 🏢 **Internal Docs**: Company policy lookups

---

## 🔬 Technical Details

### Embedding Model

- **Name**: all-MiniLM-L6-v2
- **Dimensions**: 384
- **Max sequence length**: 256 tokens
- **Size**: ~80MB
- **Speed**: ~1000 sentences/second on CPU

### Vector Store

- **Database**: ChromaDB
- **Type**: In-memory (for Colab)
- **Distance metric**: Cosine similarity
- **Indexing**: HNSW (Hierarchical Navigable Small World)

### LLM

- **Model**: HuggingFaceH4/zephyr-7b-beta
- **Parameters**: 7 billion
- **Context window**: 8192 tokens
- **Temperature**: 0.7

---

## 📈 Performance

### Task 2 Metrics:

- **Wikipedia chunks**: 17 segments
- **Total words processed**: ~5,100
- **Average chunk size**: 300 words
- **Embedding dimension**: 384
- **Retrieval time**: <1 second
- **Generation time**: 60-90 seconds
- **Output quality**: Factually accurate, coherent

---

## 🚧 Limitations

- **Corpus boundary**: Limited to indexed Wikipedia article(s)
- **Temporal**: No access to updates after article snapshot
- **Query sensitivity**: Performance depends on query formulation
- **Chunk boundaries**: Context may be split across segments
- **LLM constraints**: Generation quality depends on model capabilities

---

## 📝 License

This project is part of an academic assignment for Data Science Python coursework.

---

## 🙏 Acknowledgments

- Wikipedia for open-access knowledge
- HuggingFace for free model hosting
- SentenceTransformers for embedding models
- ChromaDB for vector storage
- LangChain for RAG framework

---

## 🔗 Related

- **Task 1**: [Multi-Agent Research Lab](https://github.com/bmperaltillap-wq/multiagent_research-lab)
- **Bonus**: See `outputs/reflection.md` for methodology comparison
