<div align="center">

# 📚 Klipin - Smart Reference & Literature Research System
> **AI-Research-TS GitHub Organization Official Documentation**

[![Bahasa Indonesia](https://img.shields.io/badge/Language-Bahasa_Indonesia-blue.svg)](README.md)
[![English](https://img.shields.io/badge/Language-English-red.svg)](README.en.md)

</div>

---

Welcome to the official profile and documentation of the **AI-Research-TS** GitHub organization. This system is designed to streamline academic article search, literature review, automated Machine Learning classification based on the Kurikulum Merdeka framework, as well as AI-powered text summarization and PDF data extraction.

---

## 🏛️ System Architecture & GitHub Repositories

The system is built using a **Decoupled 3-Tier Microservices Architecture**:

```mermaid
graph TD
    A[Frontend App - Next.js 14] -->|HTTP REST| B[Core Backend - Express.js]
    B -->|HTTP REST| C[AI Service - FastAPI / Hugging Face]
    C -->|Search API| D[SerpAPI - Google Scholar]
    C -->|ML Classification| E[LaBSE & Ensemble Models]
    C -->|Auto Summarization| F[Groq Cloud LLM - LLaMA 3.3 70B]
    B -->|Database| G[Supabase PostgreSQL]
```

### Official Organization Repositories (`AI-Research-TS`)
| Service | Tech Stack | Repository | Description |
| :--- | :--- | :--- | :--- |
| **Frontend** | Next.js 14, React, Tailwind CSS, Framer Motion | [Frontend Repository](https://github.com/AI-Research-TS/Frontend.git) | User interface featuring a visual research canvas, search history, and personal article library. |
| **Core Backend** | Node.js, Express.js, JWT, Prisma / PostgreSQL | [Backend Repository](https://github.com/AI-Research-TS/Backend.git) | REST API handling user management, authentication, article bookmarks, and AI gateway forwarding. |
| **AI Service** | Python, FastAPI, PyTorch, LaBSE, Scikit-Learn, PyMuPDF | [Ai-Services Repository](https://github.com/AI-Research-TS/Ai-Services.git) | Microservice for Kurikulum Merdeka topic classification, PDF text extraction, and LLM summarization. |

---

## ⚙️ System Prerequisites

Before running the application, ensure your environment has the following installed:
- **Node.js** v18.x or v20.x
- **Yarn** or **npm**
- **Python** v3.10.x / v3.11.x (with pip & venv)
- **Git**
- **Supabase PostgreSQL** / Local PostgreSQL instance
- **SerpAPI Key** (for Google Scholar paper searches)
- **Groq API Key** (for LLaMA 3.3 automated text summarization)

---

## 🚀 Getting Started Guide

You can choose between 2 deployment methods depending on your development requirements:

### 🌟 Method 1: Running Fully Locally (Full Local Mode)

In this mode, all 3 services (**AI Service**, **Core Backend**, and **Frontend**) run locally on your development machine.

#### Step 1: Clone All Repositories
```bash
git clone https://github.com/AI-Research-TS/Ai-Services.git
git clone https://github.com/AI-Research-TS/Backend.git
git clone https://github.com/AI-Research-TS/Frontend.git
```

#### Step 2: Launch AI Service (Python FastAPI - Port 8000)
```bash
cd Ai-Services

# 1. Create a Python virtual environment
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Create a .env file inside Ai-Services:
# SERPAPI_KEY=your_serpapi_key_here
# GROQ_API_KEY=your_groq_api_key_here
# DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres

# 4. Start the FastAPI server
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```
> The AI Service will run at: `http://127.0.0.1:8000`

#### Step 3: Launch Core Backend (Node.js Express - Port 5000)
```bash
cd ../Backend

# 1. Install dependencies
yarn install

# 2. Create a .env file inside Backend:
# PORT=5000
# DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres
# JWT_SECRET=your_jwt_secret_key
# FASTAPI_URL=http://127.0.0.1:8000

# 3. Start the Express server
yarn dev
```
> The Core Backend will run at: `http://localhost:5000`

#### Step 4: Launch Frontend App (Next.js - Port 3000)
```bash
cd ../Frontend

# 1. Install dependencies
yarn install

# 2. Create a .env.local file inside Frontend:
# NEXT_PUBLIC_API_URL=http://localhost:5000/api

# 3. Start the Next.js dev server
yarn dev
```
> Open your browser at: **`http://localhost:3000`**

---

### ☁️ Method 2: Hybrid Mode (Local Services + Hugging Face AI Cloud)

In this mode, you **do not need to install PyTorch or heavy Machine Learning model weights on your local machine**. The Core Backend connects directly to the production Hugging Face ZeroGPU Space.

```text
[Frontend: localhost:3000] ──> [Backend: localhost:5000] ──> [HF AI Service Cloud]
```

#### Step 1: Clone Backend & Frontend Repositories
```bash
git clone https://github.com/AI-Research-TS/Backend.git
git clone https://github.com/AI-Research-TS/Frontend.git
```

#### Step 2: Configure & Launch Core Backend
Navigate to the `Backend` directory and update `.env`:

```env
PORT=5000
DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres
JWT_SECRET=your_jwt_secret_key
FASTAPI_URL=https://iruuuu-ai-service-skripsi.hf.space
```

Launch the backend:
```bash
cd Backend
yarn install
yarn dev
```

#### Step 3: Configure & Launch Frontend
Navigate to the `Frontend` directory and update `.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

Launch the frontend:
```bash
cd Frontend
yarn install
yarn dev
```

Open your browser at **`http://localhost:3000`** and the application is ready to use!

---

## 🔬 AI Machine Learning Classification System

The AI Service employs an **Ensemble Learning** pipeline utilizing **LaBSE** (`sentence-transformers/LaBSE`) embeddings to classify academic literature into **45 Kurikulum Merdeka categories**:

1. **Feature Extraction**: Article titles and abstracts are encoded into 768-dimensional dense vector embeddings via LaBSE.
2. **Multi-Model Inference**: Vector embeddings are evaluated simultaneously across 4 machine learning algorithms:
   - **K-Nearest Neighbors (KNN)**
   - **Support Vector Machine (SVM)**
   - **Logistic Regression**
   - **Random Forest**
3. **Consensus & Highest Confidence Selection**: The system dynamically selects the prediction with the highest confidence score to determine the subject (`Biology`, `Physics`, `Chemistry`, `Mathematics`, `IPA`, `IPS`) and education level (`High School / SMA`, `Middle School / SMP`, `Elementary / SD`).

---

## 📝 Text Summarization & Document Extraction Module

The AI Service features an automated paper summarization engine (`src/services/summarize_service.py`) executing the following workflow:

```mermaid
graph LR
    A[Article URL / PDF] -->|PDF Auto-Discovery| B[PyMuPDF / BeautifulSoup Text Extraction]
    B -->|Clean Text & Bibliography| C[Groq LLM Engine LLaMA 3.3 70B]
    C -->|Structured Parsing| D[JSON Output Summary]
```

### Module Technical Workflow:
1. **Automated PDF Discovery (`_find_pdf_url`)**:
   Scans publisher journal pages (including Open Journal Systems / OJS) and automatically resolves direct PDF download links.
2. **Text Extraction & Reference Separation (`_extract_text_from_pdf`)**:
   Uses `PyMuPDF` (`fitz`) to parse full-text content and separate original bibliography sections.
3. **LLM Synthesis & Translation (`summarize`)**:
   Powered by **Groq Cloud API** running **`llama-3.3-70b-versatile`** to synthesize and translate foreign-language papers into formal Indonesian.
4. **Structured JSON Output Parsing**:
   Formats generated summaries into structured JSON schemas rendered on the frontend research canvas:
   - **`judul`**: Official paper title.
   - **`author`**: Authors and affiliations.
   - **`kompetensi`**: Main research goals & scope.
   - **`isi_materi`**: Core concepts & methodology.
   - **`temuan`**: Key findings, empirical data, or formulas.
   - **`kesimpulan`**: Narrative conclusion paragraph.
   - **`daftar_pustaka`**: Extracted bibliography citations.

---

## 👥 Organization & Developers
- **Organization**: `AI-Research-TS`
- **Project**: Klipin Smart Reference System
