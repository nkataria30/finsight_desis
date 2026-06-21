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

![Student dashboard — budget tracker and parental advice](docs/screenshots/student_dashboard.png)

From the trade page, students submit buy/sell requests with their reasoning and track every past submission alongside the guardian's response.

![Student trade page — submit request and trade history](docs/screenshots/student_trade_page.png)

### Guardian View
The Guardian Approval Portal gives full visibility into every trade request a linked student raises. Guardians review the reasoning behind it and approve or reject with an educational comment.

![Guardian approval portal — pending trade review](docs/screenshots/guardian_approval.png)

The Feedback History tab works as a complete audit trail of resolved trades — tracking decisions, comments, and the student's trading behavior over time.

![Guardian feedback history — resolved trades and comments](docs/screenshots/guardian_feedback.png)

### Stock Analysis
Daily prices, 1-year trends, and 5-year financial trends are synced automatically via Yahoo Finance and refreshed on a background scheduler — no manual updates needed.

![Stock price and 5-year financial trend charts](docs/screenshots/stock_price_charts.png)

An LLM turns dense financials into a plain-English investment summary — recommendation, risk level, confidence score, portfolio fit, and a balanced list of pros and cons.

![AI investment summary with portfolio fit analysis](docs/screenshots/ai_investment_summary.png)

### Portfolio Import & Diversification Scoring
Instead of entering holdings one by one, users import their entire portfolio via CSV. FinSight scores diversification out of 100 using a blend of pairwise correlation, sector entropy, and concentration penalty.

![Diversification score and sector allocation dashboard](docs/screenshots/diversification_dash.png)

Significant stock-pair correlations are surfaced directly, along with the exact score formula, so the number is never a black box.

![Significant stock correlations and score formula](docs/screenshots/correlation_table.png)

The Watchlist Impact Simulator lets users tweak share quantities on watchlist stocks and see the score shift live — before they actually buy.

![Watchlist impact simulator](docs/screenshots/watchlist_simulator.png)

### Leaderboard Dashboard
Answers the one question every investor actually wants to know — *which stocks are making money?* — ranking every holding by ROI with best/worst performers and total gain/loss at a glance.

![Leaderboard dashboard ranking holdings by ROI](docs/screenshots/leaderboard_dashboard.png)

### News Service
A dedicated FastAPI microservice aggregates live, stock-specific news from Yahoo RSS, Google News RSS, and NewsAPI, then deduplicates and sorts it by recency.

![Market news feed filtered by sentiment](docs/screenshots/news_market_feed.png)

FinBERT scores each article's sentiment and impact, so a student gets an instant read on a headline without reading the full piece.

![Per-article AI sentiment and impact analysis](docs/screenshots/news_ai_analysis.png)

### Watchlist Module
Live prices refresh every 10 seconds via yfinance, with custom price-target alerts, auto-labeled status badges (Target Hit / Near Target / Watching), and personal notes attached to each stock.

![Watchlist with live prices and price target alerts](docs/screenshots/watchlist_module.png)

### Stock Comparison Tool
Compares trade setups side by side — entry, target, stop loss, upside %, Sharpe ratio, and a composite "Best Pick" verdict — turning subjective trade ideas into a consistent, explainable comparison.

![Side-by-side stock comparison with AI recommendation](docs/screenshots/stock_comparison.png)

---

## Troubleshooting checklist
- **Migrations fail**: verify `.env` has `PGUSER/PGPASSWORD/PGHOST/PGPORT/PGDATABASE` and the `finsight` DB exists.
- **Python can’t connect to DB**: verify `.env` has `DATABASE_URL=postgresql://.../finsight`.
- **News service install is slow**: `torch` + `transformers` can take time on first install.
