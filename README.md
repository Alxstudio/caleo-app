# Caleo

> Real-time grocery price comparison for the Spanish market.

Caleo compares prices between **Mercadona** and **DIA**, helping users find the best deals and save money on every shopping trip. Built as a full-stack capstone project, it combines an LLM-powered product matching pipeline, an AI shopping assistant, and a freemium subscription model.

**Live demo:** [caleo-app-jet.vercel.app](https://caleo-app-jet.vercel.app)

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Frontend Setup](#frontend-setup)
  - [Backend Setup](#backend-setup)
- [Environment Variables](#environment-variables)
- [Database Setup](#database-setup)
- [Running with Docker Compose](#running-with-docker-compose)
- [Deployment](#deployment)
- [Known Limitations](#known-limitations)
- [Author](#author)

---

## Overview

Caleo was built to solve a simple, everyday problem: groceries cost different amounts depending on where you buy them, and comparing prices manually across supermarkets is tedious. Caleo automates that comparison, normalizes products across stores that label the same item differently, and shows users exactly how much they can save on a given shopping list.

The core engine is a **two-pass LLM normalization pipeline** (Claude Haiku + SequenceMatcher fallback) that matches 7,500 products between Mercadona and DIA with a 96.5% accuracy rate. On top of that, an integrated chatbot ("Paco", powered by Groq) answers questions using real, live database context.

## Features

- Real-time price comparison between Mercadona and DIA
- LLM-powered product normalization pipeline (96.5% match rate across 7,500 products)
- "Paco" — an AI shopping assistant with live database context injection
- Shopping list management with savings calculations
- Price history tracking
- JWT-based authentication
- Freemium subscription model (€2.99/month)
- N+1 query optimization for fast comparator performance

## Tech Stack

**Frontend**
- Next.js 14 (App Router)
- TypeScript
- Tailwind CSS
- Shadcn/ui
- Framer Motion

**Backend**
- FastAPI
- SQLAlchemy
- JWT Authentication

**Database**
- PostgreSQL (via Supabase)

**AI / LLM**
- Claude Haiku (Anthropic) — product normalization
- Groq — "Paco" chat assistant

**Deployment**
- Vercel (frontend)
- Render (backend)

## Project Structure

```
caleo-app/
├── caleo-app/              # Next.js frontend
│   ├── app/                 # App Router pages
│   ├── components/          # UI components
│   └── ...
├── backend/                 # FastAPI backend
│   ├── app/                 # API routes, models, services
│   ├── load_mercadona.py    # Mercadona data loading script
│   ├── load_dia.py          # DIA data loading script
│   ├── cross_dia.py         # Cross-matching script
│   ├── normalize_products.py # LLM normalization pipeline
│   └── requirements.txt
├── schema.sql                # Full database schema
├── docker-compose.yaml
└── README.md
```

## Getting Started

### Prerequisites

- Node.js 18+
- Python 3.11+
- PostgreSQL (or a Supabase project)
- npm / pip

### Frontend Setup

```bash
cd caleo-app
npm install
npm run dev
```

The frontend will run on `http://localhost:3000`.

### Backend Setup

```bash
cd backend
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # macOS/Linux

pip install -r requirements.txt
python run.py
```

The backend will run on `http://localhost:8000`.

## Environment Variables

**Frontend (`caleo-app/.env.local`)**

```
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
NEXT_PUBLIC_API_URL=http://localhost:8000
```

**Backend (`backend/.env`)**

```
DATABASE_URL=postgresql+psycopg2://user:password@host:5432/dbname
SECRET_KEY=your_secret_key
ANTHROPIC_API_KEY=your_anthropic_api_key
GROQ_API_KEY=your_groq_api_key
```

## Database Setup

1. Create a PostgreSQL database (locally or via Supabase).
2. Run the schema:
   ```bash
   psql -U postgres -d caleo -f schema.sql
   ```
3. Load initial product data:
   ```bash
   python load_mercadona.py
   python load_dia.py
   python cross_dia.py
   python normalize_products.py
   ```

The schema includes 13 tables: `users`, `categories`, `supermarkets`, `products`, `supermarket_products`, `price_history`, `product_mappings`, `shopping_lists`, `shopping_list_items`, `purchases`, `purchase_items`, `user_budgets`, and `llm_conversations`.

## Running with Docker Compose

```bash
docker-compose up --build
```

This spins up both the frontend and backend services as defined in `docker-compose.yaml`.

## Deployment

- **Frontend** is deployed on [Vercel](https://vercel.com).
- **Backend** is deployed on [Render](https://render.com).
- **Database** is hosted on [Supabase](https://supabase.com).

## Known Limitations

- Carrefour, Lidl, and Aldi are not included — they block scraping behind Cloudflare, and proxy access wasn't part of the project scope.
- LLM-based normalization has a real per-request cost, so scaling to more products/stores would require budget planning.
- Price history is based on the data collected during development; it does not yet reflect long-term real-world trends.

## Author

**Alex Cortell** — [GitHub @Alxstudio](https://github.com/Alxstudio)

Built as a full-stack capstone project. Now maintained as a portfolio piece.
