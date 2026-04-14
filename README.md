# 🌱 Nivesh — AI-Powered Investment Simulator

---

## 📌 Project Overview

**Nivesh** (Hindi for *Investment*) is an AI-powered personal finance and investment simulator built to help everyday Indians make smarter, more confident investment decisions — without needing a financial advisor.

The platform combines a **Monte Carlo simulation engine** with **Claude AI (Anthropic)** to give users:

- 📊 **Risk Analyzer** — probability of gain/loss across 5 Indian market portfolio types (Safe Haven, Balanced, Growth, Nifty 50, Smallcap)
- 🎲 **Interactive Simulator** — sliders for equity %, market mood (bear/neutral/bull), time horizon, and amount
- ⚖️ **Portfolio Comparison** — side-by-side simulation with Chart.js visualisation and a winner badge
- 📁 **Portfolio Manager** — create, star, and delete saved portfolios (stored in MongoDB)
- 🤖 **AI Explainer** — Claude AI explains your portfolio in plain language and gives personalised behavioral finance tips

---

## 🛠️ Tech Stack

### Frontend
| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | CSS3 (custom, no framework) |
| Logic | Vanilla JavaScript (ES6+) |
| Charts | Chart.js (CDN) |
| Auth storage | localStorage (JWT tokens) |

### Backend
| Layer | Technology |
|---|---|
| Runtime | Node.js 18+ |
| Framework | Express.js |
| Database | MongoDB + Mongoose |
| Authentication | JWT (access + refresh tokens) |
| Password hashing | bcrypt |
| AI / LLM | Anthropic Claude API |
| Real-time | WebSocket (`ws` library) |
| Security | Helmet, CORS, express-rate-limit |

---

## 📁 Project Structure

```
Nivesh/
│
├── frontend/                        # Vanilla HTML/CSS/JS — no build step
│   ├── index.html                   # Landing / Login / Register page
│   ├── app.html                     # Main dashboard (5 tabs)
│   ├── guide.html                   # Investment education guide
│   ├── login.html                   # Standalone login page
│   └── assets/                      # Images, icons, static assets
│
└── backend/                         # Node.js REST API + WebSocket
    ├── server.js                    # Entry point
    ├── .env.example                 # Environment variable template ← copy to .env
    ├── config/
    │   └── db.js                    # MongoDB connection
    ├── middleware/
    │   ├── auth.js                  # JWT protect middleware
    │   └── errorHandler.js          # Global error handler
    ├── models/
    │   ├── User.js                  # User schema
    │   └── Portfolio.js             # Portfolio + simulation history schema
    ├── routes/
    │   ├── auth.js                  # Register, login, logout, refresh
    │   ├── portfolios.js            # Portfolio CRUD
    │   ├── simulate.js              # Monte Carlo simulation engine
    │   └── ai.js                    # Claude AI explainer routes
    └── utils/
        ├── websocket.js             # Real-time simulation streaming
        └── seed.js                  # Database seeder (test users)
```

---

## ⚙️ Setup Instructions — Run Locally

### Prerequisites

- [Node.js 18+](https://nodejs.org/)
- A MongoDB instance — local or [MongoDB Atlas free tier](https://www.mongodb.com/cloud/atlas)
- An [Anthropic API key](https://console.anthropic.com/) (for AI features)

---

### Step 1 — Clone the repository

```bash
git clone https://github.com/Raghavchaw/Nivesh.git
cd Nivesh
```

---

### Step 2 — Set up the Backend

```bash
cd backend

# Install dependencies
npm install

# Create your environment file
cp .env.example .env
```

Open `.env` and fill in your values:

```env
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/nivesh
JWT_SECRET=your_long_random_secret_here
JWT_REFRESH_SECRET=your_different_long_random_secret_here
ANTHROPIC_API_KEY=sk-ant-api03-REPLACE_ME
CLIENT_URL=http://localhost:3000
```

```bash
# Start the backend server
node server.js
```

The API will be live at **http://localhost:5000**

---

### Step 3 — Set up the Frontend

The frontend is plain HTML — **no install or build step needed.**

```bash
cd ../frontend

# Option A — Python server (built-in, no install)
python3 -m http.server 3000

# Option B — Node.js
npx serve 

# Option C — VS Code
# Install "Live Server" extension → right-click index.html → Open with Live Server

# Option D — Just open the file directly
open index.html
```

Open **http://localhost:3000** in your browser.

> Make sure `const API = 'http://localhost:5000/api'` at the top of `app.html` and `index.html` points to your running backend.

---

### 🔑 Demo Credentials

After running `npm run seed` in the backend:

| Email | Password |
|---|---|
| arjun@test.com | test1234 |
| priya@test.com | test1234 |

---

## 🌐 API Reference

Base URL: `http://localhost:5000/api`

### Auth
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/auth/register` | No | Register new user |
| `POST` | `/auth/login` | No | Login — returns JWT tokens |
| `POST` | `/auth/logout` | Yes | Invalidate refresh token |
| `POST` | `/auth/refresh` | No | Get new access token |
| `GET` | `/auth/me` | Yes | Get current user profile |
| `PATCH` | `/auth/me` | Yes | Update profile |
| `PATCH` | `/auth/change-password` | Yes | Change password |
| `DELETE` | `/auth/me` | Yes | Deactivate account |

### Portfolios
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `GET` | `/portfolios` | Yes | List all user portfolios |
| `POST` | `/portfolios` | Yes | Create portfolio |
| `GET` | `/portfolios/:id` | Yes | Get single portfolio |
| `PATCH` | `/portfolios/:id` | Yes | Update portfolio |
| `DELETE` | `/portfolios/:id` | Yes | Delete portfolio |
| `POST` | `/portfolios/:id/simulation` | Yes | Save simulation result |
| `GET` | `/portfolios/:id/simulation-history` | Yes | Get simulation history |

### Simulation
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/simulate` | No | Run Monte Carlo simulation |
| `POST` | `/simulate/compare` | No | Compare two portfolio types |
| `GET` | `/simulate/presets` | No | Get all portfolio presets |

### AI
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| `POST` | `/ai/explain` | Yes | Claude explains portfolio in plain language |
| `POST` | `/ai/behavioral-tip` | Yes | Behavioral finance insight from Claude |

### WebSocket — Real-time Simulation Streaming

Connect to: `ws://localhost:5000/ws?token=YOUR_JWT_TOKEN`

**Send:**
```json
{
  "type": "stream_simulation",
  "payload": {
    "portfolioType": "balanced",
    "amount": 100000,
    "years": 5,
    "marketMood": "neutral",
    "simCount": 1000,
    "requestId": "abc123"
  }
}
```

**Receive:**
```json
{ "type": "simulation_start",    "requestId": "abc123", "total": 1000 }
{ "type": "simulation_progress", "completed": 500, "progress": 50, "buckets": [...] }
{ "type": "simulation_complete", "result": { "gainProbability": 67, "expectedReturn": 14.2 } }
```

---

## 🔑 Environment Variables

All environment variables live in `backend/.env`. See [`backend/.env.example`](./backend/.env.example) for the full template.

| Variable | Description | Required |
|---|---|---|
| `PORT` | Port the server runs on | Yes |
| `MONGODB_URI` | MongoDB connection string | Yes |
| `JWT_SECRET` | Secret for signing access tokens | Yes |
| `JWT_REFRESH_SECRET` | Secret for signing refresh tokens | Yes |
| `ANTHROPIC_API_KEY` | Anthropic Claude API key | Yes |
| `CLIENT_URL` | Frontend origin (CORS) | Yes |
| `NODE_ENV` | `development` or `production` | Yes |

The **frontend has no environment variables** — the backend URL is set via `const API = '...'` inside `app.html` and `index.html`.

---

## 🤖 External APIs & Services

### Anthropic Claude API
- **Used for:** Portfolio explanations + behavioral finance tips
- **Model:** `claude-sonnet-4-20250514`
- **Get a key:** [console.anthropic.com](https://console.anthropic.com/)
- **Docs:** [docs.anthropic.com](https://docs.anthropic.com/)

### MongoDB
- **Used for:** User accounts, portfolios, simulation history
- **Free cloud option:** [MongoDB Atlas M0](https://www.mongodb.com/cloud/atlas)

---
