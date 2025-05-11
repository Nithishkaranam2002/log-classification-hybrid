# Log Classification With Hybrid Classification Framework

This project implements a **hybrid log classification system** using FastAPI, capable of intelligently classifying log messages by combining **Regex rules**, **Sentence Transformers (BERT) + Logistic Regression**, and **LLMs (via Groq)**. It supports flexible classification of structured and unstructured log data through a REST API interface.

---

## 🧠 Project Motivation

Log data can vary from predictable patterns to highly complex, unstructured text. A single classification approach may not generalize well. This hybrid system:

* Uses **regex** for well-defined patterns
* Uses **ML** for moderately complex logs with labeled data
* Uses **LLMs** for advanced inference when training data is sparse or logs are highly contextual

---

## 🏗️ Architecture Overview

```
              +---------------------------+
              |      Client (e.g. Postman) |
              +------------+--------------+
                           |
                           v
                +----------+-----------+
                |       FastAPI Server |
                +----------+-----------+
                           |
             +-------------+-------------+
             |                           |
    +--------v--------+       +----------v-----------+
    | classify_with_  |       |  If source ==        |
    | regex()         |<------+  'LegacyCRM'         |
    +--------+--------+       |  => classify_with_llm|
             |                +----------+-----------+
             |                           |
             v                           v
    +-------------------+     +----------------------------+
    | classify_with_bert|     |  classify_with_llm (Groq)  |
    +-------------------+     +----------------------------+
```

---

## 🧪 Classification Strategy

### 1. **Regex-based Classification** (`processor_regex.py`)

* Matches fixed log patterns
* Returns a predefined label if matched, else returns `None`

### 2. **BERT + Logistic Regression** (`processor_bert.py`)

* Embeddings from `all-MiniLM-L6-v2` SentenceTransformer
* Logistic Regression model trained on labeled logs
* Returns a label if confidence > 0.5, else `Unclassified`

### 3. **LLM-based Classification** (`processor_llm.py`)

* Calls Groq's LLM (e.g., `deepseek-r1-distill-llama-70b`)
* Returns one of: `Workflow Error`, `Deprecation Warning`, or `Unclassified`

---

## 📁 Folder Structure

```
log-classification/
├── classify.py                # Main classifier logic
├── server.py                  # FastAPI server
├── processor_regex.py         # Regex-based classification
├── processor_bert.py          # BERT + LogisticRegression classification
├── processor_llm.py           # LLM-based classification (Groq)
├── log_classifier.joblib      # Trained logistic regression model
├── log_classification.ipynb   # Notebook used to train BERT+LR model
├── resources/
│   ├── test.csv               # Sample input logs
│   └── output.csv             # Output with target_label
├── requirements.txt           # Python dependencies
```

---

## 🚀 API Usage

### Endpoint

```
POST /classify/
```

### Request

* Upload a `.csv` file with headers:

  * `source`
  * `log_message`

### Response

* Returns a downloadable `.csv` file with an added column `target_label`

### Example (Postman or Swagger)

```
Key: file      | Type: File      | Value: test.csv
```

---

## 🔧 Setup Instructions

### 1. Clone the repo and install dependencies:

```bash
git clone <repo_url>
cd log-classification
pip install -r requirements.txt
```

### 2. Run the FastAPI server:

```bash
uvicorn server:app --reload
```

### 3. Access API docs:

* Swagger: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
* ReDoc: [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc)

---

## 📦 Requirements

```
fastapi
uvicorn
pandas
scikit-learn
sentence-transformers
groq
python-dotenv
joblib
python-multipart
```

---

## 📌 Notes

* Ensure your Groq API key is set in `.env`
* Test file should include at least two columns: `source`, `log_message`
* Trained model file `log_classifier.joblib` should exist in root

---

## 📈 Future Improvements

* Add UI for uploading files
* Add database logging for audit trails
* Add confidence scores in the output CSV
* Option to return JSON instead of file

---

## 👨‍💻 Author

**Dimpu Nithish Karanam**
Machine Learning Developer | Log Intelligence & Automation
