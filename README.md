# ALAMKansya

*Ang alkansya ng kaalaman.* (The piggy bank of knowledge.)

ALAMKansya is an evidence assistant for BPI Strategy & Insights (S&I). Anyone in a business unit can ask a
question in plain words, in English, Filipino or Taglish, and get a short brief written from the bank's approved
research. Every statement links to the study and page it came from.

> All studies, people and figures in this repository are synthetic. They were made up for this prototype.

## What you can do

### Ask a question, get a cited brief

Type a business question such as *"Why do SME owners drop off during online loan applications?"*. ALAMKansya
searches the approved studies your team is allowed to read, matching on meaning rather than exact words, and
writes a brief in about 10 to 20 seconds.

Each brief:

- **Leads with the answer.** You can read it as Executive, Analyst or Plain-language. The wording changes, the
  facts don't.
- **Cites everything.** Each statement links to its study and page. Key figures are copied straight from the
  studies, never written by the AI.
- **Says how sure it is.** It shows a confidence level, who was surveyed, what people said in focus groups, and
  what the evidence doesn't cover.
- **Can be printed** or saved as a PDF memo.

### Ask a follow-up

Under any brief, ask something like *"What about owners in Mindanao?"*. The follow-up stays on the same subject
and links back to the first brief.

### Get an honest "no", then an alert

If no approved study answers your question, the brief says so instead of guessing, and ALAMKansya remembers the
question. When S&I later approves a study that answers it, a green notice appears for you, along with a badge
on **Ask a question**.

### Browse studies and request access

**Studies** lists every approved study. Another team's restricted study shows only its title and a **Request
access** button. The team that owns the study decides.

### Ask an analyst

One click from any brief sends it to S&I. The question is already drafted from whatever the brief couldn't
answer, and the analyst's reply shows up on your brief.

### Check before you commission research

Paste the objectives of research you're thinking of ordering. **Before you commission** marks each one as
*Covered*, *Covered but old*, *Partly covered* or *New problem* and lists the studies behind each mark. Nothing
is sent until you send it.

### For S&I analysts

- **Review queue:** new reports wait here before anyone can search them. Each report is checked for personal
  data (phone numbers, emails, IDs, names) and for overlap with studies already approved. Analysts edit the study
  card and findings, then approve or reject the report with a note.
- **Analyst desk:** questions business units sent from their briefs, with the gaps already written up.
- **Access:** requests to read restricted studies. S&I decides for its own studies, and each team's approver
  decides for theirs.
- **Gap map:** coverage by topic and place, how often each topic is asked about, and the questions no study has
  answered yet. Use it to see where new research would be used first.
- **Audit log:** every brief, review decision, access decision and alert, with who did it and their note. Entries
  can't be edited or deleted.

## Demo personas

The demo has no sign-up. Pick a persona from the menu at the top right. Each persona sees only what their team
may read, so the same question can get different answers.

| Persona | Team · role | Try this |
|---|---|---|
| **Ben Santos** | SME Banking · business unit | Ask a question, ask a follow-up, then get alerted when a new study answers an earlier question. |
| **Dino Cruz** | SME Banking · approver | Decide other teams' requests to read SME Banking's restricted studies. |
| **Carla Mendoza** | Cards & Payments · business unit | Ask the SME question to see permissions at work, then request access to the restricted survey. |
| **Ana Reyes** | Strategy & Insights · analyst | Review and approve new reports, answer analyst questions, and open the gap map and audit log. |

The **How it works** page (`/how`) in the app covers the same ground in more detail.

### Try it in five minutes

1. As **Ben**, click the example *"Why do SME owners drop off during online loan applications?"* and read the brief.
2. Under it, ask the follow-up *"What about owners in Mindanao?"*.
3. Ask *"How do sari-sari store owners use supplier credit?"*. No approved study answers this yet.
4. Switch to **Ana** → **Review queue** → *Cebu SME Digital Onboarding Focus Groups 2026* to see the
   personal-data and overlap checks.
5. Open *Mindanao Sari-sari Store Credit Focus Groups 2026*, add a note and **Approve** it.
6. Switch back to **Ben**. A green notice says a new study answers his earlier question. Click **See the answer**.
7. As **Ana**, open **Gap map** and **Audit log**.

Approving the Mindanao report uses up this walkthrough. See [Resetting the demo](#resetting-the-demo) to run it
again.

## Good to know

- **The AI writes the wording only.** Numbers, charts and confidence levels come from the studies, and every
  sentence is checked against its source.
- **Restricted studies never reach the AI.** Row-level security in the database hides what your team may not read
  before any search runs.
- **People make the decisions.** An analyst approves every study, owning teams decide access, any brief can go to
  an analyst, and no research is commissioned until a person sends the request.
- **It works without an AI service.** By default, briefs are written from built-in templates and search uses word
  matching. Briefs are still cited, checked and filtered by permissions. You can switch to Claude and Cohere on
  Amazon Bedrock for AI-written briefs and search by meaning (see below).

---

## Project layout

| Folder | What's in it |
|---|---|
| `backend/` | FastAPI service (`app/`), the Supabase loader (`scripts/supabase_setup.py`) and a local database helper (`scripts/dev_db.py`) |
| `frontend/` | Next.js 16 web app |
| `supabase/migrations/` | Database schema, row-level security and search functions |
| `data/` | The synthetic studies and reports that are loaded into the database |
| `render.yaml` | Render Blueprint for the backend |

## Deploying

```
Browser ─▶ Vercel (Next.js frontend, optional password)
              │  /api/*  + X-Demo-Key
              ▼
           Render (FastAPI backend, free web service)
              │
              ▼
           Supabase (Postgres + pgvector)
```

<details>
<summary><b>1. Database (Supabase)</b></summary>

1. Create a Supabase project. Under **Connect**, copy the **Session pooler** connection string (port 5432).
2. Copy `backend/.env.supabase.example` to `backend/.env.supabase` and paste in the string with your password.
3. With Python 3.12:
   ```bash
   cd backend
   python -m venv .venv
   .venv/bin/pip install -r requirements.txt          # Windows: .venv\Scripts\pip
   cd ..
   backend/.venv/bin/python backend/scripts/supabase_setup.py --check
   backend/.venv/bin/python backend/scripts/supabase_setup.py
   ```
   This applies every migration and loads 33 approved studies, 6 reports waiting for review and the search data
   for all 160 findings. It's safe to run again.

</details>

<details>
<summary><b>2. Backend (Render)</b></summary>

**Blueprint:** Render → **New → Blueprint** → this repository. Fill in these values when asked:

- `DATABASE_URL`: the same Session pooler string.
- `BACKEND_SHARED_SECRET`: a long random string (e.g. `openssl rand -hex 32`). You'll also need it for Vercel.

Or create a **Web Service** by hand with root directory `backend`, build command
`pip install -r requirements.txt`, start command
`uvicorn app.main:create_app --factory --host 0.0.0.0 --port $PORT`, health check `/api/health`, and the
variables in `backend/.env.example`.

To check it: `https://<your-service>.onrender.com/api/health` should answer, and `POST /api/demo/login`
without the key should return **401**.

**Keep it awake:** Render's free service goes to sleep after about 15 idle minutes. Point a free uptime monitor
(e.g. UptimeRobot) at `/api/health` every 5 minutes.

</details>

<details>
<summary><b>3. Frontend (Vercel)</b></summary>

Vercel → **Add New → Project** → this repository. Set **Root Directory** to `frontend` (Vercel detects Next.js)
and add these environment variables (see `frontend/.env.example`):

```
BACKEND_URL=https://<your-service>.onrender.com
BACKEND_SHARED_SECRET=<the same value as on Render>
DEMO_BASIC_AUTH=judges:<choose-a-password>
```

If your plan allows it, set **Settings → Functions → Region** to Singapore (`sin1`) and redeploy.

Open the Vercel URL, sign in with the `DEMO_BASIC_AUTH` user and password, and pick a persona.

If long requests time out through Vercel, deploy the frontend as a second Render web service instead: root
`frontend`, build `npm ci && npm run build`, start `npx next start -H 0.0.0.0 -p $PORT`, and the same variables.

</details>

<details>
<summary><b>Using Claude and Cohere on Amazon Bedrock</b></summary>

You need an AWS account that can call Claude Sonnet 5.5 and Cohere Embed v4 in `ap-southeast-1`.

1. On Render, replace `LLM_PROVIDER` / `EMBEDDING_PROVIDER` with the Bedrock block in `backend/.env.example`
   and add the AWS access key.
2. Reload the database with the same search model: in `backend/.env.supabase`, set
   `EMBEDDING_PROVIDER=bedrock` and `EMBEDDING_MODEL=global.cohere.embed-v4:0`, then reset as below.

The stored search data and the questions must use the same embedding model. Otherwise search finds nothing.

</details>

### Resetting the demo

```bash
npx supabase login
npx supabase link --project-ref <your-project-ref>
npx supabase db reset --linked
backend/.venv/bin/python backend/scripts/supabase_setup.py --seed-only
```

## Running locally

You need **Python 3.12** and **Node.js 20.9+**. You don't need Supabase or any AI service: the helper script
starts a local Postgres (with pgvector) and loads the synthetic data for you.

**1. Backend** (from the repository root):

```bash
cd backend
python -m venv .venv
.venv/bin/pip install -r requirements.txt     # Windows: .venv\Scripts\pip install -r requirements.txt
.venv/bin/python scripts/dev_db.py            # starts the local database, loads the data, writes .env.local
.venv/bin/python -m uvicorn app.main:create_app --factory --port 8000
```

On Windows, use `.venv\Scripts\python` instead of `.venv/bin/python`.

**2. Frontend** (in a second terminal):

```bash
cd frontend
npm ci
npm run dev
```

Open <http://localhost:3000> and pick a persona at the top right. The frontend forwards `/api` calls to
`http://127.0.0.1:8000` by default (set `BACKEND_URL` in `frontend/.env.local` to change this).

Useful extras:

- `python scripts/dev_db.py --reset` wipes the local data and reloads it, which also resets the walkthrough.
- `python scripts/dev_db.py --stop` stops the local database.
- To use your own Postgres or Supabase instead, copy `backend/.env.example` to `backend/.env` and set
  `DATABASE_URL`.
- Don't commit `backend/.env`, `backend/.env.local`, `backend/.env.supabase` or `frontend/.env.local`. They're
  already in `.gitignore`.
