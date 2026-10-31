# Real AI Assessment — Controlled Demo Runbook

This runbook uses the existing AptoHire direct-provider implementation. It is designed for a **local synthetic-data demonstration**, not production use with real student accounts.

## Goal

Demonstrate:

1. A student submits an actual project.
2. Supabase stores the submission and queues an audit run.
3. The backend extracts evidence from the submitted project.
4. A configured LLM provider returns rubric-constrained JSON.
5. The backend validates evidence references and recomputes scores.
6. `agent_runs` records the actual provider, model, latency, token estimate and prompt version.
7. The student sees the resulting scorecard and evidence references.

## Safety boundary

The current code intentionally restricts direct assessment to fictional `.test` accounts and local synthetic opt-in. Keep this restriction. Do not edit out `isFictionalTestAccountEmail`, `isLocalSyntheticDirectOptedIn`, or the matching checks in the name of a hackathon demo.

Use fictional seed accounts such as `naman@demo.aptohire.test`, `avi@demo.aptohire.test`, or `aarav@unia.edu.test` only in a local Supabase environment. These are seed fixtures, not actual user accounts. Check the local seed and auth setup before relying on any specific account.

## A. Start local services

From `Code/aptohire-backend`:

```bash
cp .env.example .env.local
supabase start
supabase db reset
```

Use the exact local URLs and keys printed by `supabase status`. Never paste service-role keys into frontend environment files.

## B. Configure the backend for a local real-provider run

Edit `Code/aptohire-backend/.env.local` (ignored local file) and set:

```dotenv
APP_ENV=local
AGENT_MODE=direct
LLM_ALLOW_SYNTHETIC_DIRECT=true

LLM_PROVIDER_ORDER=gemini,groq,openrouter
GEMINI_API_KEY=YOUR_GEMINI_API_KEY
LLM_MODEL_AUDIT_GEMINI=YOUR_SUPPORTED_GEMINI_MODEL_ID
LLM_MODEL_FAST_GEMINI=YOUR_SUPPORTED_GEMINI_MODEL_ID

# Keep distinct random values; do not use these example labels as secrets.
CALLBACK_SECRET=REPLACE_WITH_A_RANDOM_SECRET_AT_LEAST_32_CHARS
RATE_LIMIT_PEPPER=REPLACE_WITH_A_DIFFERENT_RANDOM_SECRET
ID_HASH_PEPPER=REPLACE_WITH_A_DIFFERENT_RANDOM_SECRET
ALIAS_PEPPER=REPLACE_WITH_A_DIFFERENT_RANDOM_SECRET
```

Use the provider/model combination actually enabled for your account. Do not guess a model ID from an old tutorial. Keep provider keys in backend secrets only.

For local Edge Functions, use the `.env.local` file as required by the backend's `fn:serve` script. If you use hosted Supabase for a controlled demo, set equivalent values as Supabase Function secrets only after confirming that the target project is a non-production demo project.

## C. Set the database mode

The `AGENT_MODE` application setting is stored in `app_config` as JSON. In the local Supabase SQL editor:

```sql
update public.app_config
set value = '"direct"'::jsonb
where key = 'AGENT_MODE';

update public.app_config
set value = 'false'::jsonb
where key = 'AI_KILL_SWITCH';
```

Confirm the values:

```sql
select key, value
from public.app_config
where key in ('AGENT_MODE', 'AI_KILL_SWITCH', 'BORDERLINE', 'AI_DAILY_CAP_PER_USER')
order by key;
```

Do not disable the kill switch in a shared or production environment without explicit authorization.

## D. Point the student portal to Supabase

In `Code/aptohire-student/frontend/.env.local`:

```dotenv
VITE_APP_ENV=local
VITE_DEMO_MODE=false
VITE_USE_MOCKS=false
VITE_SITE_URL=http://localhost:5174
VITE_ROOT_URL=http://localhost:5173
VITE_SUPABASE_URL=http://127.0.0.1:54321
VITE_SUPABASE_ANON_KEY=YOUR_LOCAL_SUPABASE_ANON_KEY
```

Do not put `SUPABASE_SERVICE_ROLE_KEY`, provider API keys, `CALLBACK_SECRET`, or other privileged values in any `VITE_*` variable.

## E. Point the organization portal to the same Supabase project

In `Code/aptohire-organization/.env.local`:

```dotenv
VITE_APP_ENV=local
VITE_DEMO_MODE=false
VITE_USE_MOCKS=false
VITE_SITE_URL=http://localhost:5176
VITE_STUDENT_URL=http://localhost:5174
VITE_SUPABASE_URL=http://127.0.0.1:54321
VITE_SUPABASE_ANON_KEY=YOUR_LOCAL_SUPABASE_ANON_KEY
```

Both portals must use the same local Supabase project. If one portal points at a different backend or remains in mock mode, the journey is not end-to-end.

## F. Start the portals

Use the package scripts in each portal's own README. At minimum:

```bash
# In Code/aptohire-student
npm install
npm run dev

# In Code/aptohire-organization
npm install
npm run dev
```

Start the remaining portals only if needed for reviewer/admin actions. Never share demo credentials publicly; use the local seed/test setup and reset the database before and after the demo.

## G. Perform the AI assessment

1. Sign in as a fictional `.test` student account.
2. Create a new project submission.
3. Attach a real public GitHub repository URL that you are authorized to submit.
4. Add project documentation/source files within the existing upload limits.
5. Claim only skills genuinely demonstrated by the project.
6. Submit the project for review.
7. Open the student review-progress/scorecard page and wait for the run to complete.
8. In the admin agent-runs view or database, inspect the run provenance.

Useful SQL inspection:

```sql
select
  id,
  submission_id,
  mode,
  status,
  stage,
  provider,
  model,
  kind,
  prompt_version,
  token_estimate,
  latency_ms,
  error_code,
  started_at,
  finished_at
from public.agent_runs
order by created_at desc
limit 10;
```

Inspect the evidence manifest and audit result:

```sql
select id, submission_id, evidence_manifest, provider, model, prompt_version,
       token_estimate, latency_ms, status, stage
from public.agent_runs
order by created_at desc
limit 5;

select id, submission_id, overall_score, confidence, review_status, rubric
from public.audits
order by created_at desc
limit 5;
```

The exact column names should be checked against the current migrations if the schema has changed.

## H. Prove it was a real model call

Capture a screenshot or short recording showing:
- `mode = direct`
- `provider` equals the provider actually called (for example `gemini`)
- `model` equals the configured model ID
- non-null latency
- token counts/estimate when returned by the provider
- prompt version
- evidence manifest
- completed run status

A provider/model field alone is not enough if it was seeded or copied. Corroborate it with a fresh `agent_runs` row and server-side provider logs/usage data. If the response was served from cache, label it as a cache hit and run a new distinct submission for a fresh provider call.

## I. Evidence-linked scorecard checklist

For every scored criterion, verify:
- the evidence reference exists in the persisted run manifest;
- the cited path/line range/document is present in the submission evidence;
- the rationale is consistent with the cited evidence;
- unsupported criteria are marked insufficient rather than assigned guessed scores;
- the final score is recomputed by the backend;
- borderline or low-confidence results remain pending human review;
- only verified, shareable skills appear in recruiter discovery.

## J. Common failure causes

| Error / symptom | Likely reason | Action |
|---|---|---|
| Synthetic account forbidden | Account email is not a fictional `.test` address | Use a local seeded test account |
| Direct opt-in denied | `APP_ENV` or `LLM_ALLOW_SYNTHETIC_DIRECT` is wrong | Check backend Edge Function environment |
| Provider not configured | API key or audit model ID is missing | Set backend-only provider secret and model |
| Run fails before scoring | `AGENT_MODE` mismatch, kill switch, daily cap or config issue | Inspect safe error code and `app_config` |
| Evidence insufficient | Repo/doc sources are missing, private or not readable | Attach accessible evidence; do not invent evidence |
| Video ignored | Media is only supported by Gemini adapter and only when a valid uploaded object is available | Use supported MIME/size/duration; do not claim video was analyzed unless run metadata/evidence confirms |
| UI shows no score | Run is queued, failed, needs review or portal is in mock mode | Confirm both front-end adapter and backend state |

## Production gate — not part of this demo

Before allowing real student accounts to use model assessment, implement and review:
- explicit, revocable AI-processing consent with a clear provider/data-use notice;
- provider data-processing and retention review;
- identity redaction and prompt-data minimization tests;
- production-only feature flag with separate admin approval;
- rate/cost caps and monitoring;
- deletion/retention and incident-response procedures;
- security review of GitHub URL fetching, storage access and callback handling;
- a human appeal/review process and measured assessment quality;
- end-to-end RLS and consent tests using real backend adapters.

Do not make the production gate a last-minute hackathon shortcut.
