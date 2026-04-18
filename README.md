# ResumeAI — AI Resume Analyzer

> Upload your PDF resume and get an instant ATS score, section-by-section feedback, keyword analysis, and actionable suggestions — powered by Gemini AI.

---

## ✨ Features

- 📄 **Drag-and-drop PDF upload** (max 5MB)
- 🎯 **ATS Score (0–100)** with animated ring gauge
- 📊 **Section breakdown**: Contact Info, Summary, Skills, Experience, Education, Keywords
- 🔑 **Keyword analysis**: found vs. missing ATS keywords
- 💡 **Actionable suggestions** to improve your resume
- ✅ **Overall verdict**: Poor / Average / Good / Excellent
- 🌑 **Dark-themed, modern UI** — no frameworks, pure HTML/CSS/JS

---

## 🗂️ Project Structure

```
resume_analzer/
├── .github/workflows/ci.yml   # GitHub Actions CI/CD
├── frontend/
│   ├── index.html             # App HTML
│   ├── style.css              # Dark-themed CSS
│   └── app.js                 # Vanilla JS logic
├── backend/
│   ├── app.js                 # Express app factory
│   ├── server.js              # Server entry point
│   ├── analyzer.js            # PDF parse + Gemini API
│   └── routes/analyze.js      # POST /api/analyze route
├── tests/api.test.js          # Jest + Supertest tests
├── Dockerfile                 # Container build
├── render.yaml                # Render deployment config
├── .env.example               # Environment variable template
└── README.md
```

---

## 🚀 Quick Start (Local)

### Prerequisites
- Node.js ≥ 20
- A [Google Gemini API key](https://aistudio.google.com/)

### 1. Clone & Install

```bash
git clone https://github.com/YOUR_USERNAME/resume_analzer.git
cd resume_analzer
npm install
```

### 2. Configure Environment

```bash
cp .env.example .env
# Edit .env and set your GEMINI_API_KEY
```

### 3. Run Dev Server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

---

## 🐳 Docker (Local)

```bash
docker build -t resume-analyzer .
docker run -p 3000:3000 --env-file .env resume-analyzer
```

---

## ☁️ Deploy to Render

### One-time Setup

1. **Push to GitHub** — push this repo to your GitHub account.

2. **Create a Render account** at [render.com](https://render.com) (free tier works).

3. **New Web Service** → connect your GitHub repo.

4. Render will auto-detect `render.yaml`. Confirm:
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`

5. **Add Environment Variable** in Render dashboard:
   - Key: `GEMINI_API_KEY`
   - Value: your Gemini API key

6. Click **Deploy** — your app is live in ~2 minutes!

### CI/CD via GitHub Actions

Add these secrets to your GitHub repo (`Settings → Secrets → Actions`):

| Secret | How to get it |
|---|---|
| `GEMINI_API_KEY` | [aistudio.google.com](https://aistudio.google.com/) |
| `RENDER_DEPLOY_HOOK_URL` | Render dashboard → your service → **Settings** → **Deploy Hook** |

On every push to `main`, the pipeline will: **lint → test → build Docker → trigger Render deploy**.

---

## 🧪 Running Tests

```bash
npm test
```

Tests cover:
- Health endpoint
- File validation (no file, wrong type, too large)
- JSON parser (strips markdown fences)

---

## 🔐 Environment Variables

| Variable | Required | Description |
|---|---|---|
| `GEMINI_API_KEY` | ✅ | Your Google Gemini API key |
| `PORT` | ❌ | Server port (default: 3000, auto-set by Render) |
| `NODE_ENV` | ❌ | `development` or `production` |

---

## 🧠 How It Works

1. User uploads a PDF resume
2. `pdf-parse` extracts raw text from the PDF
3. Text is sent to **Gemini 1.5 Pro** with a structured JSON prompt
4. Gemini returns a scored analysis as JSON
5. The frontend renders the score gauge, section cards, keywords, and suggestions

---

## 📡 API Reference

### `POST /api/analyze`

**Request**: `multipart/form-data` with field `resume` (PDF file)

**Response**:
```json
{
  "score": 78,
  "verdict": "Good",
  "sections": {
    "contactInfo":  { "score": 90, "status": "good",    "feedback": "..." },
    "summary":      { "score": 55, "status": "average", "feedback": "..." },
    "skills":       { "score": 85, "status": "good",    "feedback": "..." },
    "experience":   { "score": 75, "status": "average", "feedback": "..." },
    "education":    { "score": 95, "status": "good",    "feedback": "..." },
    "keywords":     { "score": 50, "status": "average", "feedback": "..." }
  },
  "suggestions": ["Add quantified achievements...", "..."],
  "keywordsFound":   ["Python", "REST API", "Git"],
  "keywordsMissing": ["Docker", "CI/CD", "Kubernetes"]
}
```

### `GET /api/health`

Returns `{ "status": "ok" }` — used by Render for health checks.

---

## 📄 License

MIT
