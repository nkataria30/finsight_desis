# FinSight 📈
A role-based, AI-powered portfolio analytics and trading simulator designed for students and guardians. 

## 🏗️ Architecture
FinSight operates as a monorepo with four distinct microservices:
1. **Frontend (`:5173`)**: React + Vite (UI, Charts, Dashboards).
2. **Node Backend (`:3000`)**: Express.js (Auth, Guardian-Student links, Postgres DB ops).
3. **Python Core (`:8000`)**: FastAPI (Portfolio Math, MPT Analysis, OpenAI Summaries).
4. **News Service (`:8001`)**: FastAPI (RSS Scraping, FinBERT Sentiment AI).

---

## ⚙️ Prerequisites
* **Node.js** (v18+)
* **Python** (v3.12+)
* **PostgreSQL** (Running on port 5432)

---

## 🚀 1. Installation
To install all dependencies for all four services at once, simply run the setup script from the root directory:

**Windows:**
Double-click `setup.bat` or run:
```bash
.\setup.bat
```

**Mac/Linux**
```bash
cd frontend && npm install && cd ..
cd backend-node && npm install && cd ..
cd backend-python && pip install -r requirements.txt && cd ..
cd news-service && pip install -r requirements.txt && cd ..
```

## 2. Environment Variables
Create a single file named .env in the ROOT of the project (deshaw-main/.env). Do not put .env files inside the individual service folders.
```bash
# --- General ---
ENVIRONMENT=development
JWT_SECRET=your_super_secret_jwt_key_here

# --- Database (Node.js) ---
PGUSER=postgres
PGPASSWORD=YourPostgresPassword
PGHOST=localhost
PGPORT=5432
PGDATABASE=finsight

# --- Database (Python) ---
DATABASE_URL=postgresql://postgres:YourPostgresPassword@localhost:5432/finsight

# --- AI & External APIs ---
OPENAI_API_KEY=sk-your-openai-api-key
OPENAI_MODEL=gpt-4o-mini
NEWSAPI_KEY=your-news-api-key
```

## 3. Database Setup
1. Ensure PostgreSQL is running on port 5432.

2. Create a database named `finsight`.

3. Run migrations (recommended):

```bash
npm run migrate --prefix backend-node
```

This applies `database/migrations/001_...` through `004_...` automatically and uses the same `PG*` variables from your root `.env`.

## 4. First-run data sync (required for charts/AI)
After the services are running, open `http://localhost:8000/docs` and run these once:

1. `POST /companies/sync`
2. `POST /financials/sync-all/now`
3. `POST /prices/update-all`

## 5. Running the App
To start all four servers simultaneously, run the root orchestrator from the deshaw-main folder:

```Bash
npm run dev
```
(Note: This requires a root package.json configured with the concurrently package).

Expected Output:

Frontend running at http://localhost:5173

Node API running at http://localhost:3000

Python Core running at http://localhost:8000/docs

News Service running at http://localhost:8001/docs

## ✨ Features & Screenshots

### Student View
The student dashboard gives a complete financial snapshot in one place — budget utilization, pending trades awaiting guardian review, and any feedback or advice the guardian has left.
<img width="900" height="408" alt="image" src="https://github.com/user-attachments/assets/aec95eac-a065-44af-a10c-39d312f2fe93" />

From the trade page, students submit buy/sell requests with their reasoning and track every past submission alongside the guardian's response.
<img width="1832" height="832" alt="image" src="https://github.com/user-attachments/assets/f74bb1ab-615a-4554-a8af-8d44b3736abe" />

### Guardian View
The Guardian Approval Portal gives full visibility into every trade request a linked student raises. Guardians review the reasoning behind it and approve or reject with an educational comment.
<img width="1913" height="898" alt="image" src="https://github.com/user-attachments/assets/4c491fab-4c11-4ce6-9bd9-819fb0cabbeb" />

The Feedback History tab works as a complete audit trail of resolved trades — tracking decisions, comments, and the student's trading behavior over time.
<img width="1905" height="911" alt="image" src="https://github.com/user-attachments/assets/5eaaa907-7f4d-4eb5-9ba9-aa5e165a54f4" />


### Stock Analysis
Daily prices, 1-year trends, and 5-year financial trends are synced automatically via Yahoo Finance and refreshed on a background scheduler — no manual updates needed.
<img width="1116" height="957" alt="image" src="https://github.com/user-attachments/assets/111489a2-21f6-48c2-9278-5cc7fdbd0c20" />

An LLM turns dense financials into a plain-English investment summary — recommendation, risk level, confidence score, portfolio fit, and a balanced list of pros and cons.
<img width="1060" height="905" alt="image" src="https://github.com/user-attachments/assets/aa4b9cb1-105b-415c-a4fc-bc7bfb7c853a" />

### Portfolio Import & Diversification Scoring
Instead of entering holdings one by one, users import their entire portfolio via CSV. FinSight scores diversification out of 100 using a blend of pairwise correlation, sector entropy, and concentration penalty.
<img width="1918" height="761" alt="image" src="https://github.com/user-attachments/assets/878e468d-c4b2-43cf-ae0f-880fef20e1c2" />

Significant stock-pair correlations are surfaced directly, along with the exact score formula, so the number is never a black box.
<img width="1877" height="827" alt="image" src="https://github.com/user-attachments/assets/2203b119-5ab9-45e1-ba83-92900d6dfd70" />

The Watchlist Impact Simulator lets users tweak share quantities on watchlist stocks and see the score shift live — before they actually buy.
<img width="1672" height="408" alt="image" src="https://github.com/user-attachments/assets/a042a117-f2a9-4a96-8185-c3dab6ab8a78" />

### Leaderboard Dashboard
Answers the one question every investor actually wants to know — *which stocks are making money?* — ranking every holding by ROI with best/worst performers and total gain/loss at a glance.
<img width="1840" height="870" alt="image" src="https://github.com/user-attachments/assets/d4a8784f-aea6-4bb4-be92-7c6bb1f8074c" />

### News Service
A dedicated FastAPI microservice aggregates live, stock-specific news from Yahoo RSS, Google News RSS, and NewsAPI, then deduplicates and sorts it by recency.
<img width="1891" height="778" alt="image" src="https://github.com/user-attachments/assets/386fa421-e3f9-403b-a53d-efab04b49d79" />

FinBERT scores each article's sentiment and impact, so a student gets an instant read on a headline without reading the full piece.
<img width="1827" height="642" alt="image" src="https://github.com/user-attachments/assets/4d46b606-d9e9-4cd1-b6f0-7e0c43242245" />

### Watchlist Module
Live prices refresh every 10 seconds via yfinance, with custom price-target alerts, auto-labeled status badges (Target Hit / Near Target / Watching), and personal notes attached to each stock.
<img width="1890" height="937" alt="image" src="https://github.com/user-attachments/assets/0f402b9c-457e-4c7f-9979-e4228cd0c053" />

### Stock Comparison Tool
Compares trade setups side by side — entry, target, stop loss, upside %, Sharpe ratio, and a composite "Best Pick" verdict — turning subjective trade ideas into a consistent, explainable comparison.
<img width="1900" height="942" alt="image" src="https://github.com/user-attachments/assets/83997728-62d5-4813-85bc-f68ef51c5b3f" />


---

## Troubleshooting checklist
- **Migrations fail**: verify `.env` has `PGUSER/PGPASSWORD/PGHOST/PGPORT/PGDATABASE` and the `finsight` DB exists.
- **Python can’t connect to DB**: verify `.env` has `DATABASE_URL=postgresql://.../finsight`.
- **News service install is slow**: `torch` + `transformers` can take time on first install.
