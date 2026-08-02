<div align="center">

# 📚 Klipin - Smart Reference & Literature Research System
> **Reference System-TS GitHub Organization Profile README**

[![English](https://img.shields.io/badge/Language-English-red.svg)](https://github.com/AI-Research-TS/.github/blob/main/profile/README.md)
[![Bahasa Indonesia](https://img.shields.io/badge/Language-Bahasa_Indonesia-blue.svg)](https://github.com/AI-Research-TS/.github/blob/main/profile/README.id.md)

</div>

---

Selamat datang di profil resmi organisasi GitHub **Reference System-TS**. Organisasi ini menaungi seluruh layanan microservice dari ekosistem **Klipin - Reference System**, yaitu platform cerdas pencarian, penelaahan, klasifikasi otomatis berbasis Machine Learning (Kurikulum Merdeka), serta ringkasan & ekstraksi otomatis artikel ilmiah/jurnal pendidikan.

---

## 🏛️ Arsitektur Sistem & Repository GitHub

Sistem ini dibangun dengan arsitektur **3-Tier Microservices Decoupled**:

```mermaid
graph TD
    A[Frontend App - Next.js 14] -->|HTTP REST| B[Backend Utama - Express.js]
    B -->|HTTP REST| C[AI Service - FastAPI / Hugging Face]
    C -->|Search API| D[SerpAPI - Google Scholar]
    C -->|ML Classification| E[LaBSE & Ensemble Models]
    C -->|Auto Summarization| F[Groq Cloud LLM - LLaMA 3.3 70B]
    B -->|Database| G[Supabase PostgreSQL]
```

### Repository Resmi Organisasi (`Reference System-TS`)
| Service | Tech Stack | Repository | Deskripsi |
| :--- | :--- | :--- | :--- |
| **Frontend** | Next.js 14, React, Tailwind CSS, Framer Motion | [Frontend Repository](https://github.com/AI-Research-TS/Frontend.git) | Antarmuka pengguna visual canvas, history pencarian, dan pustaka artikel. |
| **Backend Utama** | Node.js, Express.js, JWT, Prisma / PostgreSQL | [Backend Repository](https://github.com/AI-Research-TS/Backend.git) | REST API manajemen user, autentikasi, simpan artikel, dan gateway AI. |
| **AI Service** | Python, FastAPI, PyTorch, LaBSE, Scikit-Learn, PyMuPDF | [Ai-Services Repository](https://github.com/AI-Research-TS/Ai-Services.git) | Service AI klasifikasi materi Kurikulum Merdeka, ekstraksi PDF, & ringkasan otomatis. |

---

## ⚙️ Prasyarat Sistem (Prerequisites)

Sebelum menjalankan aplikasi, pastikan sistem Anda telah terinstall:
- **Node.js** v18.x atau v20.x
- **Yarn** atau **npm**
- **Python** v3.10.x / v3.11.x (dengan pip & venv)
- **Git**
- Akun **Supabase PostgreSQL** / PostgreSQL lokal
- **SerpAPI Key** (untuk pencarian Google Scholar)
- **Groq API Key** (untuk modul ringkasan otomatis LLaMA 3.3)

---

## 🚀 Panduan Cara Menjalankan Sistem

Anda dapat memilih 2 metode eksekusi sesuai dengan kebutuhan pengembangan Anda:

### 🌟 Metode 1: Menjalankan Secara Penuh di Lokal (Full Local Mode)

Pada metode ini, seluruh 3 service (**AI Service**, **Backend Utama**, dan **Frontend**) dijalankan di mesin lokal Anda.

#### Step 1: Clone Seluruh Repository
```bash
git clone https://github.com/AI-Research-TS/Ai-Services.git
git clone https://github.com/AI-Research-TS/Backend.git
git clone https://github.com/AI-Research-TS/Frontend.git
```

#### Step 2: Jalankan AI Service (Python FastAPI - Port 8000)
```bash
cd Ai-Services

# 1. Buat virtual environment
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# 2. Install dependencies
pip install -r requirements.txt

# 3. Buat file .env pada folder Ai-Services:
# SERPAPI_KEY=your_serpapi_key_here
# GROQ_API_KEY=your_groq_api_key_here
# DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres

# 4. Jalankan FastAPI server
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```
> AI Service akan berjalan di: `http://127.0.0.1:8000`

#### Step 3: Jalankan Backend Utama (Node.js Express - Port 5000)
```bash
cd ../Backend

# 1. Install dependencies
yarn install

# 2. Buat file .env pada folder Backend:
# PORT=5000
# DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres
# JWT_SECRET=your_jwt_secret_key
# FASTAPI_URL=http://127.0.0.1:8000

# 3. Jalankan server backend
yarn dev
```
> Backend Utama akan berjalan di: `http://localhost:5000`

#### Step 4: Jalankan Frontend App (Next.js - Port 3000)
```bash
cd ../Frontend

# 1. Install dependencies
yarn install

# 2. Buat file .env.local pada folder Frontend:
# NEXT_PUBLIC_API_URL=http://localhost:5000/api

# 3. Jalankan Next.js dev server
yarn dev
```
> Buka browser di: **`http://localhost:3000`**

---

### ☁️ Metode 2: Mode Hybrid (Lokal + Hugging Face AI Space)

Pada metode ini, Anda **tidak perlu menginstal PyTorch / model ML yang berat di komputer lokal Anda**. AI Service mengandalkan API Hugging Face ZeroGPU Space yang sudah aktif di cloud.

```text
[Frontend: localhost:3000] ──> [Backend: localhost:5000] ──> [HF AI Service Cloud]
```

#### Step 1: Clone Repository Backend & Frontend
```bash
git clone https://github.com/AI-Research-TS/Backend.git
git clone https://github.com/AI-Research-TS/Frontend.git
```

#### Step 2: Konfigurasi & Jalankan Backend Utama
Buka folder `Backend`, buat/edit file `.env`:

```env
PORT=5000
DATABASE_URL=postgresql://postgres:password@db.supabase.co:5432/postgres
JWT_SECRET=your_jwt_secret_key
FASTAPI_URL=https://iruuuu-ai-service-skripsi.hf.space
```

Jalankan backend:
```bash
cd Backend
yarn install
yarn dev
```

#### Step 3: Konfigurasi & Jalankan Frontend
Buka folder `Frontend`, buat/edit file `.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:5000/api
```

Jalankan frontend:
```bash
cd Frontend
yarn install
yarn dev
```

Buka browser Anda di **`http://localhost:3000`** dan aplikasi siap digunakan!

---

## 🔬 Penjelasan Model Machine Learning AI Service

AI Service menggunakan pendekatan **Ensemble Learning** berbasis teks embedding **LaBSE** (`sentence-transformers/LaBSE`) yang diklasifikasikan ke dalam **45 Kelas Kurikulum Merdeka**:

1. **Feature Extraction**: Teks judul & abstrak di-encode menjadi vektor dense 768-dimensi menggunakan model LaBSE.
2. **Multi-Model Inference**: Teks dievaluasi bersamaan oleh 4 algoritma:
   - **K-Nearest Neighbors (KNN)**
   - **Support Vector Machine (SVM)**
   - **Logistic Regression**
   - **Random Forest**
3. **Consensus & Highest Confidence Selection**: Sistem secara dinamis memilih prediksi kelas dengan nilai probabilitas/confidence tertinggi untuk memastikan akurasi klasifikasi subjek (`Biologi`, `Fisika`, `Kimia`, `Matematika`, `IPA`, `IPS`) dan jenjang (`SMA`, `SMP`, `SD`).

---

## 📝 Modul Peringkasan Teks & Ekstraksi Dokumen (Text Summarization Module)

AI Service menyediakan modul peringkasan otomatis jurnal & materi pembelajaran (`src/services/summarize_service.py`) yang beroperasi melalui tahapan berikut:

```mermaid
graph LR
    A[URL Artikel / PDF] -->|PDF Auto-Discovery| B[PyMuPDF / BeautifulSoup Text Extraction]
    B -->|Clean Text & Bibliography| C[Groq LLM Engine LLaMA 3.3 70B]
    C -->|Structured Parsing| D[JSON Output Ringkasan]
```

### Tahapan Kerja Modul Peringkasan:
1. **Deteksi & Unduh PDF Otomatis (`_find_pdf_url`)**:
   Sistem memindai halaman web jurnal (termasuk Open Journal Systems / OJS) dan mengekstrak tautan dokumen PDF secara otomatis.
2. **Ekstraksi Teks & Pemisahan Referensi (`_extract_text_from_pdf`)**:
   Menggunakan `PyMuPDF` (`fitz`) to parse full-text content and separate original bibliography sections.
3. **Sintesis Bahasa Indonesia Berbasis LLM (`summarize`)**:
   Menggunakan API **Groq Cloud** dengan model **`llama-3.3-70b-versatile`** untuk menyintesis, menerjemahkan dokumen asing ke Bahasa Indonesia baku, dan menstrukturkan poin materi.
4. **Output Terstruktur (Structured JSON Output)**:
   Hasil ringkasan diparsing ke dalam struktur JSON yang siap ditampilkan di canvas penelitian frontend:
   - **`judul`**: Judul resmi naskah ilmiah/materi.
   - **`author`**: Penulis/peneliti naskah.
   - **`kompetensi`**: Tujuan utama & fokus masalah.
   - **`isi_materi`**: Konsep dasar & metodologi.
   - **`temuan`**: Temuan inti, fakta penting, atau rumus.
   - **`kesimpulan`**: Narasi kesimpulan dalam bentuk paragraf utuh.
   - **`daftar_pustaka`**: Referensi/sitasi penting dari dokumen asli.

---

## 👥 Organisasi & Pengembang
- **Organization**: `Reference System-TS`
- **Project**: Klipin Smart Reference System
