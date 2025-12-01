# Notebook-First Clinical Assistant — AI Agent (Google ADK + Google Cloud API)

![workflow](assets/clinical-assistant-cover.png)

A notebook-native, **Google Cloud–powered** clinical AI agent built for:

- Medical AI research & education  
- Clinical text analysis using Retrieval-Augmented Generation (RAG)  
- EMR exploration using secure Google Cloud services  
- Prototyping safe AI automations directly from notebooks  

This project uses **Google ADK** (google-cloud-* libraries) and runs fully in **Jupyter, Colab, or Kaggle**.

> ⚠️ **Not for autonomous clinical decision-making.**  
> Validate outputs with clinicians and run only in secure environments.

---

## 📘 Features Overview

### 🔍 Retrieval-Augmented Generation (RAG)
- Lightweight TF retriever (offline, no dependencies)
- Optional Vertex AI Embeddings
- Optional FAISS vector index

### 🩺 Clinical Tooling
- Lab abnormality interpreter  
- Medical text de-identification  
- Structured clinical prompt builder  
- Mock offline LLM for reproducible demos

### ☁️ Google Cloud ADK Integration
- Google Cloud Storage (GCS)
- BigQuery
- Secret Manager
- Vertex AI (LLM + Embeddings)
- Cloud SQL (optional)

---

## 🏗️ Architecture Diagram

![architecture](assets/architecture-diagram.png)

### Workflow

User Query
↓
Retriever (TF / Embeddings)
↓
Clinical Tools (labs, filters, de-identify)
↓
Prompt Builder → Vertex AI (optional)
↓
Summary + Next Steps + Logs (BigQuery/GCS)

yaml
Copy code

---

## 📂 Project Structure

clinical-assistant/
│
├── notebooks/
│ ├── simple_google_adk_notebook.ipynb
│ ├── clinical_assistant_gcp_adk_notebook.ipynb
│ └── advanced_agent_notebook.ipynb
│
├── clinical_assistant/
│ ├── retriever_tf.py
│ ├── vertex_client.py
│ ├── clinical_tools.py
│ ├── orchestrator.py
│ └── safety.py
│
├── tests/
│ ├── test_retriever.py
│ ├── test_tools.py
│ └── test_orchestrator.py
│
├── data/
├── assets/
├── README.md
└── requirements.txt

yaml
Copy code

---

## ⚙️ Installation

```bash
pip install google-cloud-storage google-cloud-bigquery google-cloud-secret-manager google-cloud-aiplatform
🔑 Authentication
✔️ Colab
python
Copy code
from google.colab import auth
auth.authenticate_user()
✔️ Local Jupyter
bash
Copy code
gcloud auth application-default login
✔️ Kaggle
Upload a private dataset containing your service_account.json:

python
Copy code
os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "/kaggle/input/sa/service-account.json"
☁️ Google Cloud API Usage (Examples)
🗃️ 1. Google Cloud Storage (GCS)
python
Copy code
from google.cloud import storage
client = storage.Client()

bucket = client.bucket("my-bucket")
blob = bucket.blob("clinical/input.txt")
blob.upload_from_filename("/content/input.txt")
🧬 2. BigQuery
python
Copy code
from google.cloud import bigquery
bq = bigquery.Client()

df = bq.query("SELECT * FROM clinical.emr LIMIT 10").to_dataframe()
df.head()
🔐 3. Secret Manager
python
Copy code
from google.cloud import secretmanager
sm = secretmanager.SecretManagerServiceClient()

name = "projects/PROJECT_ID/secrets/db_pass/versions/latest"
secret = sm.access_secret_version(name=name).payload.data.decode()
🤖 4. Vertex AI (LLM)
python
Copy code
from google.cloud import aiplatform
aiplatform.init(project=PROJECT_ID, location="us-central1")

model = aiplatform.TextGenerationModel.from_pretrained("text-bison@001")
result = model.predict("Summarize: ...", max_output_tokens=256)
🔍 TF-Based Offline RAG Retriever
python
Copy code
def tf_retrieve(query, vocab, vectors, top_k=3):
    tokens = re.findall(r"[a-z0-9]+", query.lower())
    qv = [0]*len(vocab)
    for t in tokens:
        if t in vocab:
            qv[vocab[t]] += 1
    
    scores = [(doc, cosine(qv, vectors[doc])) for doc in vectors]
    return sorted(scores, key=lambda x: x[1], reverse=True)[:top_k]
🤖 AI Agent Orchestrator
graphql
Copy code
User Query → Retrieval → Tools → Prompt → LLM → Output
python
Copy code
def orchestrate(query, labs=None):
    hits = tf_retrieve(query, vocab, vectors, top_k=2)
    evidence = [clean_text(doc['text']) for doc in hits]
    prompt = build_prompt(query, evidence, labs)
    return call_vertex_llm(prompt)
🧪 Unit Tests
retriever → ensures deterministic ranking

clinical tools → verify lab interpretations

orchestrator → structured output validation

nginx
Copy code
pytest tests/
🛡️ Safety, Compliance, Governance
De-identify PHI before passing text to Vertex AI

Use Secret Manager instead of environment variables

Use private buckets and IAM roles with least privileges

Log queries, retrieved docs, model responses (secure audit log)

Validate outputs with licensed clinicians

Never use as final medical advice

📸 Example Screenshots (replace with your own)



🎯 Ideal For
Academic healthcare AI capstone projects

Medical NLP research

EMR analytics

Safe LLM prototyping in controlled environments

Google Cloud + AI learning projects

✔️ Summary
This project provides a transparent, explainable, and Google Cloud–powered clinical AI assistant entirely within a notebook environment.
It is ideal for research, learning, prototyping, and healthcare data workflows.

