# 🔐 RAG + AES: Secure AI Chatbot for Confidential Documents
This project implements a **secure and efficient Retrieval-Augmented Generation (RAG) pipeline** using **quantized Hugging Face language models** and **AES-based encryption**. It enables secure, document-aware question answering using local `.pdf` and `.docx` files, with AI responses encrypted for safety.

Combining **Large Language Models**, **document retrieval**, and **end-to-end encryption**, this notebook offers a novel pipeline ideal for privacy-preserving enterprise or medical use cases.

---

## 💡 What This Project Does

1. **Loads confidential documents** (.pdf/.docx) from your local system.
2. **Converts documents to embeddings** using `llama-index` and Hugging Face LLMs.
3. **Accepts natural language questions** and performs **contextual retrieval**.
4. **Generates responses** using a **quantized transformer model**.
5. **Encrypts the AI output** using AES (`Fernet` from the `cryptography` module).
6. **Decrypts** only when authorized, ensuring complete information security.

---

## 📁 Code Breakdown

### 1️⃣ Library Installation and Import

Your notebook begins with installing and importing key libraries for:

* **Language modeling**: `transformers`, `bitsandbytes`, `accelerate`
* **Document processing**: `docx2txt`, `pypdf`
* **Encryption**: `cryptography` and `pycryptodome`
* **Indexing and retrieval**: `llama-index` for document ingestion and semantic retrieval

```python
!pip install llama-index transformers accelerate bitsandbytes
!pip install llama-index-llms-huggingface llama-index-embeddings-huggingface
!pip install docx2txt pypdf==4.0.1 cryptography pycryptodome
```

### 2️⃣ Quantized LLM Configuration

You configure the LLM to **load in 4-bit quantized format** for efficient performance using `BitsAndBytesConfig`.

```python
quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
)
```

This significantly reduces GPU memory usage, allowing large models to run on consumer-grade hardware.

---

### 3️⃣ Document Loading and Embedding

Using `SimpleDirectoryReader`, documents placed in a local folder are read, parsed, and passed to the embedding model.

```python
reader = SimpleDirectoryReader("./data")
documents = reader.load_data()
```

These documents are converted into **semantic vector representations** and stored in a **VectorStoreIndex** for retrieval.

```python
index = VectorStoreIndex.from_documents(documents, service_context=service_context)
```

---

### 4️⃣ Retrieval-Augmented Generation Pipeline

You set up a **retrieval system + prompt template + Hugging Face LLM**, so when a user asks a question:

1. The relevant document chunks are retrieved.
2. The prompt is formatted with context.
3. The LLM generates a context-aware answer.

```python
query_engine = index.as_query_engine()
response = query_engine.query("Your question here")
```

---

### 5️⃣ AES Encryption of Output

The generated output is **encrypted using AES encryption via Fernet** from the `cryptography` module:

```python
from cryptography.fernet import Fernet
key = Fernet.generate_key()
cipher = Fernet(key)
encrypted = cipher.encrypt(response.encode())
```

And it can be safely decrypted only with the correct key:

```python
decrypted = cipher.decrypt(encrypted)
```

This makes the entire RAG response pipeline **secure-by-design**.

---

## ✅ Key Features

* 📂 **Document-aware Q\&A**: Upload `.pdf` or `.docx` and ask natural language questions.
* 🧠 **LLM-powered retrieval**: Combines search + generation using Hugging Face models.
* ⚡ **4-bit quantization**: Efficient memory use for running large models locally.
* 🔐 **AES Encryption**: Ensures only authorized parties can read AI responses.
* 🧩 **Modular structure**: Easily extendable for APIs, web apps, or enterprise systems.

---

## 🛠️ Setup Instructions

### 🔧 Installation

```bash
pip install llama-index transformers accelerate bitsandbytes
pip install llama-index-llms-huggingface llama-index-embeddings-huggingface
pip install docx2txt pypdf==4.0.1 cryptography pycryptodome
```

### 📂 Folder Structure

```
project/
│
├── data/                  # Place your PDF or DOCX files here
├── RAG_Based_AI_with_AES.ipynb
└── README.md
```

### ▶️ Run the Notebook

```bash
jupyter notebook RAG_Based_AI_with_AES.ipynb
```

---

## 📊 Sample Use

```python
# Encrypting a message
key = Fernet.generate_key()
cipher = Fernet(key)
encrypted = cipher.encrypt(b"AI-generated answer")

# Decrypting the message
decrypted = cipher.decrypt(encrypted)
```

---

## 👥 Contributors

* **Rithvika T**
* **Monish P**
* **Manni Chellappan Ramu**

---

## 📌 License

This project is licensed under the Apache License 2.0.

