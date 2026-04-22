# 🚀 Hybrid Log Classification Framework

This project implements a professional **Hybrid Cascade Classification** system. By combining deterministic rules, classical Machine Learning, and Generative AI (LLMs), it optimizes for speed, cost, and high accuracy across varying log complexities.

## 🧠 System Architecture (The Cascade)
To ensure maximum efficiency, the system processes logs through three specialized layers:

1.  **Tier 1: Regular Expression (Regex)** *Handles predictable, fixed-format patterns (e.g., "User logged in").*
2.  **Tier 2: Sentence Transformer (BERT) + Logistic Regression** *Manages complex semantic patterns where sufficient training data exists. Uses `all-MiniLM-L6-v2` with a confidence threshold.*
3.  **Tier 3: LLM (Llama-3 via Groq API)** *The fallback layer for poorly-labeled or highly ambiguous data (e.g., LegacyCRM logs) using advanced reasoning.*


![architecture](resources/arch.png)
---

## 📂 File Overview

Based on the refactored structure, here is how the project is organized:

### 📍 Root Directory
* **`server.py`**: The FastAPI entry point. Defines the `/classify/` endpoint and manages file uploads/downloads.
* **`Dockerfile`**: Instructions for containerizing the application for production deployment.
* **`requirements.txt`**: List of Python dependencies (FastAPI, Scikit-learn, Sentence-Transformers, Groq, etc.).
* **`.env`**: Stores sensitive API keys (e.g., `GROQ_API_KEY`). *Note: This file is ignored by Git for security.*

### 📍 `src/` (Core Logic)
* **`classify.py`**: The main coordinator that decides which tier (Regex, BERT, or LLM) should handle each log message.
* **`processor_regex.py`**: Implementation of rule-based classification.
* **`processor_bert.py`**: Logic for generating BERT embeddings and running the Logistic Regression model.
* **`processor_llm.py`**: Integration with Groq API to utilize Llama-3.3-70B for complex classification.
* **`__init__.py`**: Makes the `src` folder a valid Python package.

### 📍 Data & Models
* **`models/`**: Contains the saved `.joblib` files for the trained Logistic Regression model.
* **`resources/`**: Stores test CSV files and the generated `output.csv` results.

---

## 🛠 Setup & Execution

### Option A: Docker Deployment (Recommended)
This is the fastest way to run the service in a production-ready environment:
1.  **Build the Image:**
    ```bash
    docker build -t log-classifier .
    ```
2.  **Run the Container:**
    ```bash
    docker run -p 8000:8000 --env-file .env log-classifier
    ```

### Option B: Local Manual Setup
1.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
2.  **Run the Server:**
    ```bash
    uvicorn server:app --reload
    ```

---

## 📊 Usage

1.  Navigate to `http://127.0.0.1:8000/docs` to open the **Swagger UI**.
2.  Use the `POST /classify/` endpoint.
3.  **Upload:** A CSV file with `source` and `log_message` columns.
4.  **Download:** The classified CSV containing a new `target_label` column.

## 🔧 Technologies Used
* **Backend:** FastAPI, Uvicorn
* **AI/ML:** Sentence-Transformers (BERT), Scikit-learn
* **Generative AI:** Groq API (Llama-3.3-70B-Versatile)
* **DevOps:** Docker (Linux-slim environment)
