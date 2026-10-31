# AptoHire — Judge-Focused Pitch Story and Demo Script

## Core narrative

**A student should not need a prestigious college name to prove what they can do.**

AptoHire turns actual project work into evidence-linked skill signals, then lets employers discover that evidence before the student's identity is disclosed.

## 3-minute pitch structure

### 0:00–0:25 — The human problem

“Imagine two students applying for the same entry-level developer role. One has a well-known college name and a polished résumé. The other has built a working product but comes from a college recruiters rarely recognize. Their actual abilities may be similar, but the first screening often starts with credentials and claims—not inspectable work.”

Avoid claiming that every recruiter behaves this way. Frame it as the problem AptoHire is designed to address.

### 0:25–0:45 — The insight

“Skills are easy to claim and harder to verify. AptoHire changes the unit of trust: from a résumé statement to project evidence.”

### 0:45–1:45 — Show one powerful product journey

1. A student submits a real project repository and supporting material.
2. The AI audit extracts evidence and maps it to a fixed rubric.
3. The backend validates every evidence reference and stores model provenance.
4. The student sees an explainable scorecard; insufficient evidence or borderline results go to human review.
5. A recruiter searches anonymized candidates using relevant verified skills.
6. The recruiter requests only the information needed.
7. The student grants or declines scoped, time-limited access.
8. The recruiter sees only what was approved.

Use a live direct-provider run if available. If using synthetic seed accounts, say so clearly: “This is a controlled synthetic-account demo using a real model call and the real backend workflow.”

### 1:45–2:15 — Why it is different

- Evidence links instead of unsupported skill labels.
- Blind discovery before identity reveal.
- Student-controlled scopes and expiry.
- Human review and appeal paths for uncertain or disputed results.
- Backend enforcement rather than front-end promises.

### 2:15–2:40 — Market and business model

“India has a large higher-education base, but our initial market is not every student or every employer. We are testing a focused university-led pilot where placement teams can use structured project evidence to support readiness workflows. Employer discovery is the next side of the network.”

Use only verified market sources and clearly label TAM/SAM/SOM as assumptions until named buyer research validates them.

### 2:40–3:00 — Ask

“We are looking for a small number of university placement teams and early-career recruiters to validate one measurable outcome: does evidence-linked skill review help them understand candidate capability more efficiently, without removing student control?”

## Demo shot list (3–5 minutes)

| Time | Screen | What must be real |
|---|---|---|
| 0:00–0:25 | Student submission | Supabase submission record |
| 0:25–1:20 | AI run and scorecard | Fresh provider call, evidence manifest, persisted audit |
| 1:20–1:55 | Evidence detail | Evidence ref resolves to source |
| 1:55–2:30 | Recruiter job + blind candidate list | Same Supabase backend, no identity leakage |
| 2:30–3:05 | Student access request | Persisted request and selected scopes |
| 3:05–3:35 | Recruiter reveal | Backend returns only granted scopes |
| 3:35–4:00 | Withdraw/expiry + audit | Subsequent access denied and event recorded |
| 4:00–4:30 | Validation dashboard / roadmap | Only measured results; unknowns labelled TBD |

## What to remove from the main pitch

Do not spend the majority of the pitch touring five separate portals. The portals are implementation surfaces, not the core value proposition.

Keep:
- one architecture slide;
- one trust/privacy slide;
- one AI development workflow slide for the hackathon's AI-first criterion;
- one market-validation slide;
- one roadmap slide.

Move detailed portal feature lists, every future feature and the complete architecture to appendix slides.

## Judge questions and concise answers

**Is the assessment really AI-powered?**  
“The backend includes a direct provider adapter for Gemini, Groq and OpenRouter, with schema validation and evidence-manifest checks. Our current direct assessment is restricted to local synthetic accounts; our demo uses a real provider call under that controlled restriction. We are not claiming production assessment for real student accounts yet.”

**How do you know the AI score is accurate?**  
“We have evidence-grounding and insufficient-evidence safeguards in code. Independent human-vs-AI benchmark results are still being collected; we will publish agreement, evidence precision/recall and failure cases before making accuracy claims.”

**Is the recruiter flow real?**  
“The shared backend includes anonymized candidate-card views, request-access, student consent, scoped reveal, expiry/withdrawal and audit logging. We demonstrate the flow using the real Supabase adapters and synthetic accounts, not mock transitions.”

**Do you have product-market fit?**  
“No. We have a problem hypothesis and an initial survey whose response export still needs analysis. Our next milestone is a focused university/employer pilot with defined success metrics.”

**Why five portals?**  
“They serve distinct actors, but the product story is one trust workflow. The architecture separates interfaces; the value is the evidence-to-consent journey.”

## The closing line

**“AptoHire helps employers see what a student can do before deciding who that student is.”**
