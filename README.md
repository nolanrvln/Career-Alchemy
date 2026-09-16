# CareerAlchemy ⚗️

AI-powered job discovery, resume/cover-letter tailoring, and application tracking — built as a human-in-the-loop copilot, not a spam bot.

CareerAlchemy ingests job postings from multiple sources, scores them against your real profile, uses an LLM to tailor your resume and cover letter to each posting, and helps you (semi-)automate the application itself — with a review step before anything gets submitted.

⚠️ Before you use this
Many job boards (LinkedIn, Indeed, Glassdoor) prohibit automated scraping in their Terms of Service and actively ban bot accounts. This project defaults to official APIs/RSS feeds where possible and treats HTML scraping as an opt-in, higher-maintenance source.
The tailoring engine is instructed to rephrase and reprioritize your real experience — never invent skills, employers, or dates. Always review generated documents before submitting; you are responsible for the accuracy of anything you send to an employer.
Auto-submission ships off by default. Applications are generated as drafts requiring explicit approval.
✨ Features
🔎 Multi-source job ingestion — official APIs (Adzuna, USAJobs, Greenhouse, Lever) + optional scraping via Playwright
🧠 Relevance scoring — embedding similarity between job description and your profile, so you're not tailoring resumes for irrelevant roles
✍️ AI tailoring engine — rewrites resume bullets and drafts a cover letter per job, grounded in a structured "truth file" of your real experience
📄 Document generation — clean, ATS-friendly DOCX/PDF output
✅ Review UI — approve, edit, or reject each tailored application before it goes out
🖱️ Semi-automated submission — Playwright autofills application forms; you click submit
📊 Application tracker — kanban-style board (Applied → Interview → Offer → Rejected)
🏗️ Architecture
Job Sources:APIs/RSS/ScrapersIngestion + Dedup +StorageRelevance FilterTailoring Engine: LLMReview UISubmission: Autofill / ATSAPIApplication Tracker
🧱 Tech stack
Layer	Tech
Backend API	Python, FastAPI
Task queue	Celery + Redis
Database	PostgreSQL + pgvector
Scraping/autofill	Playwright
LLM	Claude API (or GPT-4.x)
Embeddings	Voyage / OpenAI / Claude embeddings
Document generation	python-docx, WeasyPrint
Frontend	Next.js, React, Tailwind CSS
Storage	S3 / Cloudflare R2
Deployment	Docker Compose, Fly.io / Railway
📁 Project structure
careeralchemy/
├── backend/
│   ├── app/
│   │   ├── api/            # FastAPI routes
│   │   ├── ingestion/      # Source connectors (Adzuna, Greenhouse, scrapers)
│   │   ├── tailoring/      # LLM prompt logic, truth-file handling
│   │   ├── documents/      # Resume/cover letter rendering
│   │   ├── submission/     # Playwright autofill workers
│   │   ├── models/         # SQLAlchemy models
│   │   └── main.py
│   ├── tasks/               # Celery task definitions
│   └── tests/
├── frontend/
│   ├── app/                 # Next.js pages
│   └── components/
├── docker-compose.yml
├── .env.example
└── README.md
🚀 Getting started
Prerequisites
Python 3.11+
Node.js 20+
Docker & Docker Compose
An Anthropic (or OpenAI) API key
API keys for job sources you want to use (e.g. Adzuna)
Setup
bash
# Clone the repo
git clone https://github.com/nolanrvln/careeralchemy.git
cd careeralchemy

# Copy environment template
cp .env.example .env
# then fill in: DATABASE_URL, REDIS_URL, ANTHROPIC_API_KEY, ADZUNA_APP_ID, ADZUNA_APP_KEY, etc.

# Start infra (Postgres, Redis)
docker compose up -d db redis

# Backend
cd backend
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
alembic upgrade head          # run DB migrations
uvicorn app.main:app --reload

# Celery worker (separate terminal)
celery -A app.tasks worker --loglevel=info

# Frontend (separate terminal)
cd ../frontend
npm install
npm run dev

Visit http://localhost:3000 to open the dashboard.

Set up your profile ("truth file")

Before tailoring can run, fill in your real work history/skills at backend/app/tailoring/truth_file.example.json and save it as truth_file.json. This is the single source of truth the LLM is grounded on — it will not fabricate experience beyond what's in here.

🔧 Configuration
Env var	Description
ANTHROPIC_API_KEY	LLM API key for tailoring engine
DATABASE_URL	Postgres connection string
REDIS_URL	Redis connection string for Celery
ADZUNA_APP_ID / ADZUNA_APP_KEY	Adzuna job API credentials
ENABLE_SCRAPING	true/false — toggles Playwright scraper sources
AUTO_SUBMIT	false by default — requires explicit opt-in
🗺️ Roadmap
 Phase 1: Core ingestion + tailoring pipeline
 Phase 2: Document generation + tracker UI
 Phase 3: Semi-automated submission (Playwright, human-approved)
 Phase 4: Additional source connectors, refined relevance scoring
🤝 Contributing

PRs welcome. Please open an issue first for any change to the submission/automation logic, since that's the most legally and ethically sensitive part of the codebase.

📄 License

MIT.
