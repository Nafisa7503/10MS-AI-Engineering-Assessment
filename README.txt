Retrieval-Augmented Generation (RAG) System for Bangla & English

This project implements a multilingual Retrieval-Augmented Generation (RAG) system using Bangla HSC textbooks as the corpus. It allows users to ask natural language questions and get context-aware answers in either Bangla or English.



Features

-Supports Bangla & English queries.
-Uses multilingual embedding and QA models.
-Evaluates answer quality with groundedness and relevance.
-Stores and utilizes short-term memory for conversational continuity.



Setup Guide

1. Clone & Install Dependencies

```bash
pip install flask sentence-transformers transformers faiss-cpu scikit-learn nltk
```

2. Download NLTK Tokenizer

```python
import nltk
nltk.download('punkt')
```

3. Prepare Data

Place a `cleaned_text.txt` file in the root directory. This file should contain the cleaned corpus text.

4. Run the Server

```bash
python RAG.py
```

---

Tools, Libraries, and Models Used

| Tool/Library             | Purpose                                         |
| ------------------------ | ----------------------------------------------- |
| Flask                    | Web API endpoints                               |
| SentenceTransformer      | `intfloat/multilingual-e5-large` for embeddings |
| FAISS                    | Fast vector similarity search                   |
| HuggingFace Transformers | QA model: `xlm-roberta-base-squad2`             |
| Scikit-learn             | Cosine similarity calculation                   |
| NLTK                     | Sentence tokenization                           |


Sample Queries

📄 POST `/ask`

Body:

```json
{
  "query": কাকে অনুপমের ভাগ্য দেবতা বলে উল্লেখ করা হয়েছে?"
```

Response:

```json
{
  "query": "কাকে অনুপমের ভাগ্য দেবতা বলে উল্লেখ করা হয়েছে?",
  "answer": "মামা",
  "grounded": true,
  "relevance": 0.8123,
  "chat_history": [
    ["কাকে অনুপমের ভাগ্য দেবতা বলে উল্লেখ করা হয়েছে??", "মামা"]
  ]
}
```

---

## 🔄 API Documentation

| Endpoint  | Method | Description                   |
| --------- | ------ | ----------------------------- |
| `/ask`    | POST   | Ask a question, get an answer |
| `/health` | GET    | Health check for the server   |

---

Evaluation Metrics

Groundedness: Checks if the answer is present in the retrieved context.
Relevance Score: Average cosine similarity between query embedding and retrieved chunk embeddings.

---

Project Q\&A

What method/library did you use to extract the text?

-At first I retrieved only the text part and removed all the other parts that seemed irrelevant for the training of the model: "cleaned_text.txt"
-Library used: No external library just Python’s built-in io module (implicit).

Did you face formatting challenges?

Yes. As there are not very advanced model for Bengali, this embedding and chunking seemed slighlty difficult causing the model to retrieve wrong chunk for some questions. 

What chunking strategy did you use?

Line-based, 10 lines per chunk.
Ensures enough semantic context while keeping chunk size FAISS-friendly.

What embedding model did you use and why?

-`intfloat/multilingual-e5-large`
-Works well with both Bangla and English.
-Embeddings are normalized for inner product search with FAISS.

How are you comparing queries to chunks?

-Cosine similarity using normalized embeddings.
-FAISS (`IndexFlatIP`) for fast nearest-neighbor search.

-How do you ensure meaningful comparison?

-Embeddings are generated from the same model.
-Retrieves top-k chunks most similar to the query.
-QA model uses both retrieved chunks and short-term memory.

What happens with vague queries?

-May result in low relevance score or hallucinated answers.
-Indicated by `grounded=False` or `relevance < 0.6`.

-Are results relevant? How to improve?

-Generally yes for specific queries. However, some queries had produced irrelevant outputs due to mismatch of chunks. 
-Improvements:

  -Sentence-based overlapping chunking
  -Better preprocessing (e.g. paragraph detection)
  -Reranker model


