# Aegis

![Aegis Logo](./assets/landing.png)

![Python](https://img.shields.io/badge/-Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-000000?logo=nextdotjs&logoColor=fff&style=flat-square)
![React](https://img.shields.io/badge/-React-61DAFB?style=flat-square&logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=fff&style=flat-square)
![Flask](https://img.shields.io/badge/-Flask-000000?style=flat-square&logo=flask&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-06B6D4?logo=tailwindcss&logoColor=fff&style=flat-square)
![Supabase](https://img.shields.io/badge/Supabase-3FCF8E?logo=supabase&logoColor=fff&style=flat-square)
![Google Gemini](https://img.shields.io/badge/Google%20Gemini-8E75B2?logo=google&logoColor=fff&style=flat-square)
![Hugging Face](https://img.shields.io/badge/Hugging%20Face-FFD21E?logo=huggingface&logoColor=000&style=flat-square)
![Framer Motion](https://img.shields.io/badge/Framer%20Motion-0055FF?logo=framer&logoColor=fff&style=flat-square)

Multi-layer AI jailbreak detection system. Protect your AI applications from prompt injection attacks with advanced pattern matching, machine learning, and LLM-powered analysis.

Demo - https://www.youtube.com/watch?v=7WD2ltKzzRM
---

## Features

- **3-Layer Detection** - Regex patterns, ML classification, and LLM analysis
- **Prompt Rewriting** - Automatically sanitize suspicious prompts while preserving intent
- **Real-time Analysis** - Sub-second response times with intelligent caching
- **Risk Scoring** - Confidence-based threat assessment (0-100 scale)
- **API Key Management** - Secure authentication with usage tracking and analytics
- **Interactive Playground** - Test detection capabilities with live JSON output
- **Comprehensive Analytics** - Per-key metrics tracking requests, latency, flags, and success rates
- **Glassmorphic UI** - Modern design with animations and magnetic cursor effects

---

## In-App Snapshots

![Playground](./assets/playground.png)
*Playground - Test detection in real-time*

![Dashboard](./assets/dashboard.png)
*Dashboard - Manage API keys and view analytics*

---


#### 1. Backend Setup

```bash
# Create virtual environment
python -m venv technica
source technica/bin/activate  # Windows: technica\Scripts\activate

# Install dependencies
pip install flask flask-cors python-dotenv google-generativeai supabase transformers torch

# Run Flask server
python tests.py  # Runs on port 5000
```

**Required Python Packages:**
- `flask` - Web framework
- `flask-cors` - CORS handling
- `python-dotenv` - Environment variables
- `google-generativeai` - Gemini API client
- `supabase` - Database client
- `transformers` - HuggingFace models
- `torch` - PyTorch for ML inference

#### 2. Frontend Setup

```bash
cd aitector-frontend
npm install
npm run dev  # Runs on port 3000
```

**Key Dependencies:**
- `next@16.0.3` - React framework
- `react@19.2.0` - UI library
- `@supabase/supabase-js` - Auth & database
- `framer-motion@12.23.24` - Animations
- `tailwindcss@4` - Styling
- `typescript@5` - Type safety

#### 3. Database Setup

**Supabase SQL Schema:**

```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email TEXT UNIQUE NOT NULL,
  created_at TIMESTAMP DEFAULT now()
);

-- API Keys table
CREATE TABLE api_keys (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  key_hash TEXT NOT NULL,
  usage_count INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT now(),
  last_used_at TIMESTAMP
);

-- API Usage table
CREATE TABLE api_usage (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  api_key_id UUID REFERENCES api_keys(id) ON DELETE CASCADE,
  endpoint TEXT NOT NULL,
  latency_ms INTEGER,
  flagged BOOLEAN,
  risk_score FLOAT,
  success BOOLEAN,
  iterations INTEGER,
  created_at TIMESTAMP DEFAULT now()
);

-- Enable Row Level Security
ALTER TABLE api_keys ENABLE ROW LEVEL SECURITY;
ALTER TABLE api_usage ENABLE ROW LEVEL SECURITY;

-- RLS Policies
CREATE POLICY "Users can view own keys" ON api_keys
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can create own keys" ON api_keys
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete own keys" ON api_keys
  FOR DELETE USING (auth.uid() = user_id);
```

---

## Project Structure

```
technica-2025/
├── tests.py                      # Flask backend (main server)
├── .env                          # Environment variables
├── assets/
│   ├── landing.png               # Landing page screenshot
│   ├── playground.png            # Playground screenshot
│   └── dashboard.png             # Dashboard screenshot
│
├── aitector-frontend/            # Next.js frontend
    ├── src/
    │   ├── app/
    │   │   ├── page.tsx          # Landing page
    │   │   ├── layout.tsx        # Root layout with favicon
    │   │   ├── globals.css       # Global styles
    │   │   ├── auth/
    │   │   │   └── page.tsx      # Authentication page
    │   │   ├── dashboard/
    │   │   │   └── page.tsx      # Dashboard with API keys
    │   │   ├── playground/
    │   │   │   └── page.tsx      # Interactive testing
    │   │   ├── docs/
    │   │   │   └── page.tsx      # API documentation
    │   │   ├── pricing/
    │   │   │   └── page.tsx      # Pricing tiers
    │   │   └── api/
    │   │       ├── keys/         # API key CRUD endpoints
    │   │       ├── analytics/    # Analytics endpoint
    │   │       └── auth/         # Supabase auth callback
    │   │
    │   ├── components/
    │   │   ├── navbar.tsx        # Navigation bar
    │   │   ├── footer.tsx        # Footer component
    │   │   ├── hero.tsx          # Hero section
    │   │   ├── feature-card.tsx  # Feature cards
    │   │   ├── cursor-corners.tsx# Magnetic cursor
    │   │   └── ui/
    │   │       ├── button.tsx    # Button component
    │   │       └── card.tsx      # Card component
    │   │
    │   └── lib/
    │       ├── supabaseclient.ts # Supabase client setup
    │       ├── useSupabaseAuth.tsx # Auth hooks
    │       └── utils.ts          # Utility functions
    │
    ├── public/
    │   ├── small.png             # Favicon
    │   └── full.png              # Logo
    │
    ├── package.json              # Dependencies
    ├── tsconfig.json             # TypeScript config
    ├── tailwind.config.ts        # Tailwind config
    └── next.config.ts            # Next.js config
              # Python virtual environment
```

---

## API Reference

### `/detect` (POST)

**Headers:**
```
Authorization: Bearer <api_key>
Content-Type: application/json
```

**Body:**
```json
{
  "prompt": "Your input text to analyze"
}
```

**Response:**
```json
{
  "classifier": "jailbreak",
  "flagged": true,
  "llm": {
    "is_jailbreak": true,
    "confidence": 0.95,
    "explanation": "Contains system override attempt"
  },
  "patterns": ["system_override", "role_break"],
  "latency_ms": 342
}
```

**Status Codes:**
- `200 OK` - Detection completed
- `400 Bad Request` - Missing prompt
- `401 Unauthorized` - Invalid API key
- `500 Internal Server Error` - Detection failed

### `/replace` (POST)

**Headers:**
```
Authorization: Bearer <api_key>
Content-Type: application/json
```

**Body:**
```json
{
  "prompt": "Suspicious prompt to rewrite"
}
```

**Response:**
```json
{
  "original_prompt": "ignore previous instructions...",
  "rewritten_prompt": "Please help me with...",
  "iterations": 2,
  "success": true,
  "latency_ms": 1250
}
```

**Iteration Logic:**
- Max 5 attempts
- Returns original if all iterations fail
- Tracks convergence in analytics

### `/api/analytics/<key_id>` (GET)

**Headers:**
```
Authorization: Bearer <supabase_access_token>
```

**Response:**
```json
{
  "detect": {
    "total": 1523,
    "flagged": 342,
    "latency": 285.4,
    "risk": 42.3
  },
  "replace": {
    "total": 89,
    "success": 76,
    "latency": 1120.5,
    "iterations": 2.3
  }
}
```
