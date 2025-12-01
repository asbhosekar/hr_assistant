# HR Q&A Assistant with LangChain

An intelligent HR policy question-answering system powered by LangChain, OpenAI, and FAISS vector search. Extract HR policy clauses from documents, embed them, and retrieve relevant policies based on natural language queries.

## Features

- **Policy Extraction**: Automatically extract HR policy clauses from text using LLMs
- **Vector Embeddings**: Convert policy text into embeddings for semantic search
- **FAISS Indexing**: Fast similarity search across policy documents
- **LLM Integration**: Use OpenAI's GPT models for intelligent clause extraction
- **Robust Error Handling**: Graceful fallbacks when dependencies are unavailable
- **Mock Mode**: Run without API keys for testing and development
- **REST API**: Flask-based API for easy integration and testing in Postman

## Project Structure

```
HR_Assistant/
├── CaseStudy6_LangChain_HR_QA_Notebook_Regenerated_SafeTemplate.ipynb
├── hr_policies_sample.csv
├── extracted_clauses_safe.json
├── faiss_hr_clauses_index.index
├── faiss_hr_clauses_index_vectors.json
└── README.md
```

## Installation

### Prerequisites

- Python 3.8+
- pip or conda

### Required Packages

Install all dependencies:

```bash
pip install langchain openai faiss-cpu tiktoken flask pandas
```

Or use the notebook's built-in installer:

```python
%pip install langchain openai faiss-cpu tiktoken flask pandas
```

### Environment Setup

Set your OpenAI API key:

```bash
# Windows (PowerShell)
$env:OPENAI_API_KEY = "your-api-key-here"

# Linux/macOS
export OPENAI_API_KEY="your-api-key-here"
```

## Usage

### In Jupyter Notebook

1. Open `CaseStudy6_LangChain_HR_QA_Notebook_Regenerated_SafeTemplate.ipynb`
2. Run cells sequentially to:
   - Load and configure the LLM
   - Extract HR policy clauses
   - Generate embeddings
   - Create FAISS index
   - Retrieve relevant policies based on queries

### Running the Flask API

Start the REST API server by running the last cell in the notebook:

```python
# This starts the Flask server on http://localhost:5000
```

### API Endpoints

#### 1. Health Check

```
GET /health
```

**Response:**
```json
{
  "status": "ok",
  "timestamp": "2025-12-01T10:30:00.123456"
}
```

#### 2. Query HR Policies

```
POST /api/hr-qa
Content-Type: application/json

{
  "query": "What is the parental leave policy?",
  "k": 3
}
```

**Response:**
```json
{
  "query": "What is the parental leave policy?",
  "results_count": 2,
  "results": [
    {
      "text": "Parental Leave Policy: Employees are entitled to 12 weeks of parental leave...",
      "metadata": {
        "id": 1,
        "title": "Parental Leave Policy",
        "contact": "hr@example.com"
      },
      "score": 0.85
    }
  ],
  "timestamp": "2025-12-01T10:30:05.234567"
}
```

#### 3. Extract Clauses from Text

```
POST /api/extract
Content-Type: application/json

{
  "text": "Employees accrue 12 sick days per year; medical certificate required for >3 days."
}
```

**Response:**
```json
{
  "text": "Employees accrue 12 sick days per year; medical certificate required for >3 days.",
  "clauses_count": 1,
  "clauses": [
    {
      "id": 1,
      "title": "Sick Leave Policy",
      "summary": "Employees earn 1 day of sick leave each month, capped at 12 days annually...",
      "keywords": ["sick leave", "medical certificate"],
      "contact": "hr@example.com"
    }
  ],
  "timestamp": "2025-12-01T10:30:10.345678"
}
```

## Testing in Postman

### 1. Health Check

- **Method**: GET
- **URL**: `http://localhost:5000/health`
- **Headers**: None
- **Body**: None

### 2. Query HR Policies

- **Method**: POST
- **URL**: `http://localhost:5000/api/hr-qa`
- **Headers**: `Content-Type: application/json`
- **Body**:
```json
{
  "query": "sick leave policy",
  "k": 3
}
```

### 3. Extract Clauses

- **Method**: POST
- **URL**: `http://localhost:5000/api/extract`
- **Headers**: `Content-Type: application/json`
- **Body**:
```json
{
  "text": "Remote work allowed up to 2 days per week with manager approval."
}
```

## Notebook Cells Overview

| Cell | Purpose |
|------|---------|
| 1 | Title & Introduction |
| 2 | Install Dependencies |
| 3 | Import Libraries & Setup |
| 4 | Create Safe Extract Template |
| 5 | Parse Clauses Function |
| 6 | Extraction Invocation Function |
| 7 | Extract Sample Policies |
| 8 | Save Clauses to JSON |
| 9 | Load Clauses |
| 10 | Prepare Texts for Embedding |
| 11 | Generate Embeddings |
| 12 | Create FAISS Index |
| 13 | Retrieval Demo |
| 14 | Flask API Server |

## Key Components

### Extract Template

Safely formatted prompt template for LLM-based clause extraction with escaped braces:

```
Extract 1-3 important HR policy clauses from the text below.
Return ONLY a JSON array with objects...
```

### Clause Structure

Each extracted clause follows this schema:

```json
{
  "id": 1,
  "title": "Policy Title",
  "summary": "One-sentence summary",
  "keywords": ["keyword1", "keyword2"],
  "contact": "hr@company.com"
}
```

### Retrieval Process

1. Convert query to embedding
2. Search FAISS index for nearest neighbors
3. Return top-k matching policy clauses
4. Format results with metadata

## Features & Fallbacks

- **API Key Present**: Uses real OpenAI embeddings and GPT models
- **API Key Missing**: Uses mock data and random embeddings (deterministic, seed=42)
- **FAISS Unavailable**: Falls back to JSON-based vector storage
- **Robust JSON Parsing**: Handles multiple JSON formats and malformed responses

## Example Queries

Try these queries in Postman:

```json
{
  "query": "parental leave eligibility",
  "k": 2
}
```

```json
{
  "query": "sick leave medical certificate",
  "k": 3
}
```

```json
{
  "query": "remote work policy approval",
  "k": 2
}
```

## Configuration

### Embedding Dimension

Default: 1536 (OpenAI embedding dimension)

Modify in cell 11:
```python
EMBED_DIM = 1536
```

### Model Configuration

Modify in cell 6 (`invoke_extraction_for` function):

```python
model_name: str = 'gpt-4o-mini'  # Change model
temperature: float = 0.0  # Adjust creativity (0.0 = deterministic)
max_tokens: int = 600  # Max response length
```

## Troubleshooting

### FAISS Library Issues

If FAISS fails to import:
```bash
pip install faiss-cpu
# or for GPU support
pip install faiss-gpu
```

### API Key Not Found

```python
import os
os.environ['OPENAI_API_KEY'] = 'your-key'
```

### Connection Refused (Postman)

1. Ensure Flask cell is running (last cell)
2. Check port 5000 is not in use
3. Default URL: `http://localhost:5000`

### Empty Query Results

- Ensure vectors are generated (cell 11)
- Check FAISS index exists (cell 12)
- Verify query text is meaningful

## Performance Tips

1. **Batch Queries**: Group similar queries to reuse embeddings
2. **Increase k**: Use larger k value to retrieve more results
3. **Embedding Cache**: Reuse query embeddings when possible
4. **Index Optimization**: Rebuild FAISS index periodically with new policies

## Future Enhancements

- [ ] Support for multiple document formats (PDF, DOCX)
- [ ] Database persistence (PostgreSQL, MongoDB)
- [ ] Authentication and authorization
- [ ] Rate limiting and logging
- [ ] Batch processing endpoint
- [ ] Admin interface for managing policies
- [ ] Multi-language support

## License

This project is open source and available under the MIT License.

## Support

For issues or questions, please refer to the notebook documentation or create an issue in the repository.

---

**Created with LangChain + OpenAI + FAISS**
