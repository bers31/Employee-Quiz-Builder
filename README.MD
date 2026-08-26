# 99 Group AI Property Knowledge Quiz Builder

## Overview
Tool AI untuk generate quiz knowledge industri property bagi karyawan 99 Group, dari input topic bebas.

## Problem
Karyawan 99 Group butuh cara cepat menguji/memperkuat pemahaman knowledge property (market update, KPR, skill agent, dst) tanpa tim membuat soal manual satu-satu.

## Solution
Aplikasi Streamlit yang menerima topic + preferensi (role, jumlah soal, difficulty, bahasa), memanggil Gemini API untuk generate quiz terstruktur, memvalidasinya (programmatic + AI QA) dengan retry otomatis, lalu menyajikannya sebagai quiz interaktif dengan scoring.

## Features
- Input topic, target role, jumlah soal, difficulty, bahasa
- Generate quiz dengan Gemini API (structured output)
- Validasi programmatic (jumlah soal, duplicate, opsi kosong/duplikat, difficulty match)
- AI Quality Assurance (LLM call kedua untuk relevansi & kualitas soal)
- Retry otomatis (maks 3x) kalau validasi/QA gagal
- Quiz interaktif dengan scoring otomatis dan explanation

## Architecture
User Input → Prompt Builder → Gemini API (generate) → Programmatic Validation → AI QA
→ (lolos → tampilkan quiz interaktif) / (gagal → retry, maks 3x)
→ User jawab → Scoring → Feedback + explanation

## AI Workflow
Dua LLM call terpisah per percobaan generate:
1. Generate quiz (structured output, dipaksa sesuai schema Pydantic `Quiz`)
2. AI QA - LLM kedua mereview hasil LLM pertama untuk kualitas semantik

## Prompt Strategy
Prompt lengkap ada di `prompts/quiz_generation_prompt.md`. Bentuk JSON tidak dijelaskan di teks prompt - itu diatur terpisah lewat `response_schema` Gemini API, supaya instruksi tidak redundan.

## Validation
Programmatic (`src/validation.py`): jumlah soal harus tepat sesuai request, tidak ada duplicate question, tidak ada opsi jawaban kosong/duplikat, difficulty tiap soal harus cocok dengan yang diminta. `correct_answer` dijamin valid A/B/C/D oleh schema Pydantic (`Literal` type) sejak level API.

## Quality Assurance
LLM call kedua (`src/qa_checker.py`) mengecek relevansi ke topic, distractor yang terlalu jelas benar, ambiguitas, dan konsistensi explanation - hal yang tidak bisa dicek logika biasa.

## Testing
[ISI SETELAH TESTING] - isi tabel dari Phase 13:

| Topic | Jumlah diminta | Berhasil | Catatan |
|---|---|---|---|
| | | | |

## What Went Wrong
[ISI SETELAH TESTING] - catat masalah nyata yang benar-benar muncul saat Anda testing (kalau ada). Kalau tidak ada masalah sama sekali, tulis itu apa adanya.

## Improvements Made
[ISI SETELAH TESTING] - perubahan yang benar-benar Anda lakukan merespons masalah di atas (kalau ada).

## Known Limitations
- Jumlah retry dibatasi 3x - kalau semuanya gagal, user harus generate ulang manual
- Belum ada rate-limiting sisi aplikasi - generate berulang cepat bisa kena rate limit Gemini free tier
- State quiz hilang kalau browser di-refresh (belum ada penyimpanan permanen)

## Future Improvements
- Simpan riwayat quiz per user
- Export quiz ke PDF/Word
- Dashboard hasil untuk manajer

## Setup
1. Clone repo: `git clone <url_repo_ini>`
2. Buat virtual environment: `python -m venv .venv`
3. Aktifkan: `.venv\Scripts\Activate.ps1` (Windows) / `source .venv/bin/activate` (Mac/Linux)
4. Install dependencies: `pip install -r requirements.txt`

## Environment Variables
Buat file `.env` di root project (lihat `.env.example`):
GEMINI_API_KEY=your_api_key_here

Dapatkan API key gratis di https://aistudio.google.com/apikey

## Running Locally
streamlit run app.py

## Deployment
Di-deploy ke Streamlit Community Cloud. `GEMINI_API_KEY` diset lewat secrets management Streamlit Cloud (bukan file `.env`, yang memang tidak ikut ke-commit).

## Example Inputs
[ISI SETELAH TESTING] - contoh topic yang dipakai saat testing, misal: "Property Market Updates in Jakarta", role Property Agent, 5 soal, Beginner, Bahasa Indonesia.

## Example Outputs
[ISI SETELAH TESTING] - paste 1 contoh question object asli dari hasil generate Anda.