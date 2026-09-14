# MockTest Platform — Phase 1 Setup Guide

## Your project structure

```
mocktest-platform/
├── backend/
│   ├── generator/
│   │   ├── prompts.py            ← All prompt templates
│   │   ├── question_generator.py ← Calls Claude API, returns questions
│   │   ├── validator.py          ← Two-layer AI validation
│   │   └── template_builder.py   ← Your cost-saving template engine
│   ├── database/
│   │   ├── schema.sql            ← Run this in Supabase SQL editor
│   │   └── db.py                 ← Supabase helper
│   ├── seed_questions.py         ← Populates full question bank
│   ├── requirements.txt
│   └── .env.example
```

---

## Step 1 — Copy environment file

```bash
cd mocktest-platform/backend
cp .env.example .env
```

Now open `.env` and fill in your keys:
- `ANTHROPIC_API_KEY` — from console.anthropic.com
- `OPENAI_API_KEY` — from platform.openai.com (for cross-validation)
- `SUPABASE_URL` — from your Supabase project Settings > API
- `SUPABASE_KEY` — the anon/public key from same page

SUPABASE
projectname: mocktest-platform
passward: AnilSoft#Cloud2026
---

## Step 2 — Install dependencies

```bash
cd mocktest-platform/backend
pip install -r requirements.txt
```

---

## Step 3 — Set up the database

1. Go to your Supabase project
2. Click **SQL Editor** in the left sidebar
3. Paste the entire contents of `database/schema.sql`
4. Click **Run**
5. You should see 7 tables created

---

## Step 4 — Test question generator (quick check)

```bash
cd mocktest-platform/backend/generator
python question_generator.py
```

Expected output: 4 questions printed (2 numerical + 1 reasoning + 1 English)

---

## Step 5 — Test validator

```bash
cd mocktest-platform/backend/generator
python validator.py
```

Expected output: 2 questions validated with Layer 1 + Layer 2 results

---

## Step 6 — Test template builder + shuffler

```bash
cd mocktest-platform/backend/generator
python template_builder.py
```

Expected output: 1 original SI question + 3 free variations with different numbers

---

## Step 7 — Test full pipeline (generate → validate → save)

```bash
cd mocktest-platform/backend/database
python db.py
```

Expected output: 2 questions saved to Supabase, visible in your Table Editor

---

## Step 8 — Seed the full question bank

```bash
cd mocktest-platform/backend
python seed_questions.py
```

This takes 20–30 minutes and populates ~200 validated questions.
After this, mock test generation costs near zero.

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `ANTHROPIC_API_KEY not found` | Check your .env file is in backend/ folder |
| `supabase.exc.APIError` | Check SUPABASE_URL and SUPABASE_KEY |
| `json.JSONDecodeError` | Claude returned non-JSON — re-run, it's rare |
| `openai.AuthenticationError` | Add OPENAI_API_KEY or Layer 2 skips gracefully |

---

## What we build next (Phase 2)

Once the question bank is seeded:
- Value shuffler API endpoint
- Test assembly engine (picks 100 questions, applies shuffling)
- FastAPI server that serves a complete test on demand
