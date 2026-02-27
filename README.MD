# DU Analysis — Durg University Result Analytics Platform

A full-stack analytics platform that scrapes, processes, and visualizes exam results from **Hemchand Yadav University (Durg, CG, India)** — enabling students, teachers, and administrators to search individual results, compare college performance, and identify academic trends at scale.

[![Live Dashboard](https://img.shields.io/badge/Live_Dashboard-analysis.durguniversity.workers.dev-blue?style=for-the-badge)](https://analysis.durguniversity.workers.dev)
[![API](https://img.shields.io/badge/API-Hugging_Face_Spaces-yellow?style=for-the-badge)](https://sameerbanchhor-work-durg-university-result-api.hf.space)
[![Colab Notebook](https://img.shields.io/badge/Notebook-Google_Colab-orange?style=for-the-badge)](https://colab.research.google.com/drive/1zW0SSNIBrZJ-Oiclgw2DD9cRar9RV1iI)

---

## About the Project

This is a **final-year college project** built at **Kalyan College, Bhilai, Durg (CG)**. The goal is to bring **data-driven insights** to university examination results — something that traditionally only exists as individual HTML result pages on the university website.

### What it does

- **Scrapes** thousands of individual student result pages from the university portal (with permission)
- **Parses & structures** the raw HTML into clean, queryable JSON datasets
- **Analyzes** the data using Python (Pandas) to generate insights on pass rates, subject difficulty, college rankings, and more
- **Serves** the processed data through a fast REST API
- **Visualizes** everything through a beautiful, interactive web dashboard with dark mode support

---

## Key Features

| Feature | Description |
|---|---|
| **Student Search** | Look up any student's complete result by roll number or name |
| **College Analytics** | Compare pass rates, average scores, and merit lists across colleges |
| **Subject Diagnostics** | Identify the toughest and easiest subjects based on real performance data |
| **Progression Tracking** | Track average score trends across Year 1 → Year 2 → Year 3 |
| **Merit Lists** | Auto-generated UG and PG toppers per course, per college |
| **Dark / Light Mode** | Full theme support across all dashboard pages |
| **Fast API** | In-memory indexed lookups for O(1) roll number searches |

---

## Project Architecture

```
durg results/
│
├── frontend/                    # Dashboard UI (Cloudflare Workers)
│   ├── public/
│   │   ├── index.html           # Home — search by roll number or name
│   │   ├── student.html         # Individual student result view
│   │   ├── analysis.html        # University & college-level analytics
│   │   ├── status.html          # Result status overview
│   │   └── theme.js             # Dark/light mode toggle
│   ├── wrangler.jsonc           # Cloudflare Workers config
│   └── package.json
│
├── backend/                     # REST API (FastAPI + Hugging Face Spaces)
│   ├── main.py                  # All API endpoints & analytics logic
│   ├── requirements.txt         # Python dependencies
│   └── Dockerfile               # Container config for HF Spaces
│
├── scraping/                    # Data collection & processing
│   ├── gather_dataset/          # Jupyter notebook for bulk scraping
│   ├── scrape_from_HTML/        # Python parsers for UG & PG results
│   │   ├── UG_result_scraper.py
│   │   ├── PG_result_scraper.py
│   │   └── generate_directory.py
│   └── other_analysis_stuff/    # Deep analysis & report scripts
│       ├── analysis.py
│       └── deep_analysis_report.py
│
├── schemas/                     # Data model documentation
│   ├── PG_schema.md             # PG result JSON schema & examples
│   ├── json_data_model.json     # Formal JSON data model
│   └── result_dataset_directory.txt
│
├── roll number structure.xlsx   # Reference: DU roll number format guide
└── README.MD
```

---

## Getting Started

### Prerequisites

- **Python 3.12+**
- **Node.js 18+** (for the frontend dev server)
- **Wrangler CLI** (installed automatically via `npm`)

### 1. Clone the Repository

```bash
git clone https://github.com/work-Sameerbanchhor/DU-Analysis.git
cd DU-Analysis
```

### 2. Run the Backend API Locally

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

The API documentation will be available at **http://localhost:8000/docs** (Swagger UI).

### 3. Run the Frontend Locally

```bash
cd frontend
npm install
npm run dev
```

The dashboard will be available at **http://localhost:8787**.

### 4. Using Docker (Backend)

```bash
cd backend
docker build -t du-api .
docker run -p 7860:7860 du-api
```

---

## API Endpoints

The backend exposes the following REST API endpoints:

### Core Search

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/student/{roll_number}` | Fetch a student's full result by roll number |
| `GET` | `/api/v1/search?name=...` | Search students by name (min 3 chars) |
| `GET` | `/api/v1/filter?status=...&course=...&college=...` | Filter results by status, course, or college |

### University-Wide Analytics

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/analytics/overview` | Overall pass rates, status breakdown, averages |
| `GET` | `/api/v1/analytics/colleges` | Rank all colleges by pass percentage |
| `GET` | `/api/v1/analytics/subjects` | Subject-level difficulty diagnostics |

### Deep College Analytics

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/v1/analytics/college/{college_code}` | Full analytics payload for a single college — merit lists, grade distribution, progression, toughest/easiest subjects |

> **Tip:** All analytics endpoints support optional `?course=` and `?college=` query parameters for filtered views.

---

## Data Pipeline

The data flows through three stages:

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│   1. SCRAPING    │────▸│   2. PARSING     │────▸│   3. SERVING     │
│                  │     │                  │     │                  │
│  Jupyter Notebook│     │  Python Scripts  │     │  FastAPI + Web   │
│  downloads HTML  │     │  extract JSON    │     │  Dashboard       │
│  result pages    │     │  from raw HTML   │     │  visualizes data │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

1. **Scraping** — The notebook in `scraping/gather_dataset/` downloads individual student result HTML pages from the university portal
2. **Parsing** — Scripts in `scraping/scrape_from_HTML/` parse the HTML into structured JSON using the schemas defined in `schemas/`
3. **Analysis & Serving** — The `backend/` API loads all JSON datasets into memory at startup and serves analytics via REST endpoints. The `frontend/` dashboard consumes this API

### Using the Pre-Scraped Dataset

If you don't want to scrape from scratch, extract the included `datasets.zip` (~800 MB) which contains all the pre-downloaded HTML result pages.

### Roll Number Structure

See `roll number structure.xlsx` for a detailed breakdown of how Durg University roll numbers are formatted — useful for understanding the data and building queries.

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | HTML, CSS, JavaScript (vanilla) |
| **Hosting (Frontend)** | Cloudflare Workers |
| **Backend** | Python, FastAPI, Uvicorn |
| **Hosting (Backend)** | Hugging Face Spaces (Docker) |
| **Data Processing** | Python, Pandas, BeautifulSoup |
| **Notebooks** | Jupyter / Google Colab |

---

## Live Links

| Resource | URL |
|---|---|
| **Dashboard** | [analysis.durguniversity.workers.dev](https://analysis.durguniversity.workers.dev) |
| **API** | [sameerbanchhor-work-durg-university-result-api.hf.space](https://sameerbanchhor-work-durg-university-result-api.hf.space) |
| **Colab Notebook** | [Google Colab Link](https://colab.research.google.com/drive/1zW0SSNIBrZJ-Oiclgw2DD9cRar9RV1iI) |

---

## Contributing

Contributions, suggestions, and bug reports are welcome! Here's how you can help:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

> If you want to adapt this system for **your own university**, feel free to reach out!

---

## License

This project is open source. If you use it for your institution, please give credit and consider contributing back.

---

## Author

**Sameer Banchhor**
- Kalyan College, Bhilai, Durg (CG), India
- Currently also working with **CSVTU** to improve their result systems

---

<p align="center">
  <i>Built to make university result data more accessible, transparent, and actionable.</i>
</p>
