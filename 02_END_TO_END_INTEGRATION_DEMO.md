# End-to-End Integration — Student to Recruiter

## Demo objective

Prove one complete journey using the real Supabase adapters and backend services:

**Student project → real AI audit → evidence-linked scorecard → blind recruiter search → access request → student consent → scoped identity reveal.**

The demo should use fictional seed accounts and a local/staging Supabase project. This preserves the current synthetic-only assessment guard while demonstrating actual provider and backend execution.

## The source-backed service chain

| Step | Portal / service | Source implementation | Proof to capture |
|---|---|---|---|
| 1. Submit project | Student portal | `aptohire-student/frontend/src/pages/SubmissionPage.tsx`; `features/projects/useProjects.ts`; backend `submit-project` Edge Function | Submission ID and queued run ID |
| 2. Build evidence | Backend worker | `aptohire-backend/supabase/functions/_shared/agents/evidence-bundle.ts` | Evidence manifest with refs and limitations |
| 3. Call model | Direct provider adapter | `_shared/agents/providers/direct.ts`; `_shared/agents/audit-direct.ts` | Provider/model/latency/token metadata |
| 4. Validate and persist | Callback + SQL | `agent-callback/index.ts`; `postprocess-audit.ts`; `complete_agent_run` / `apply_audit` migrations | Completed run and audit row |
| 5. View scorecard | Student portal | `ScorecardPage.tsx`, scorecard components and Supabase backend | Criterion score, evidence ref, review status |
| 6. Recruiter matching | Organization portal | `match-job/index.ts`; `org_candidate_cards` view | Blind alias, match reasons and eligible verified skills |
| 7. Request access | Organization portal | `request-access/index.ts` | Request ID and requested scope |
| 8. Student consent | Student portal | `respond-access` Edge Function and `useSharing.ts` | Granted scopes and expiry |
| 9. Reveal approved details | Organization portal | `reveal-student/index.ts` | Only granted fields, expiring links and audit event |

## Demo preparation

1. Run local Supabase and reset the database with the backend seed.
2. Configure `AGENT_MODE=direct` and local synthetic direct opt-in as described in `01_REAL_AI_ASSESSMENT_DEMO_RUNBOOK.md`.
3. Configure both student and organization portals with `VITE_USE_MOCKS=false`.
4. Confirm both portals point to the same Supabase URL and anon key.
5. Confirm test accounts exist in local Auth and have the correct `profiles.role`, `students`, `organizations` and membership rows.
6. Use a project submission with evidence that the model can actually inspect. A public GitHub repository and concise project document are preferable.
7. Use an approved demo organization and an open job with matching required skills.
8. Make sure the candidate's visibility/discovery setting is enabled and that their verified skill scores are shareable.
9. Record the state of the database before the demo so each change can be correlated to a row.

## Exact demo sequence

### Scene 1 — Student proof

Show the student profile without leading with college, name or résumé. Open the project submission page and submit a real project repository and documentation.

Narration:
> “This candidate is not asking us to trust a list of claimed skills. They are submitting work that can be inspected.”

Show the queued submission ID and status.

### Scene 2 — AI assessment

Show the real model run, not a pre-populated mock scorecard. Open the run record and point to:
- direct execution mode;
- actual provider and model;
- prompt version;
- evidence manifest;
- latency and token estimate;
- final audit status.

Open the scorecard. Select one criterion and trace its evidence reference back to a file or document.

Narration:
> “The model proposes a score, but it cannot invent the evidence. The backend accepts only evidence references from the manifest and recalculates the weighted score.”

If the score is borderline or evidence is insufficient, show the human-review state rather than presenting it as verified.

### Scene 3 — Blind discovery

Switch to the organization portal using a separate fictional recruiter account. Open a real open job and run matching.

Show a candidate alias and skills/evidence-based reasons. Confirm that the card does not show name, email or exact university identity before consent.

Narration:
> “The recruiter discovers the evidence and skill fit first—not the candidate’s name or college brand.”

### Scene 4 — Scoped access request

Request only the minimum scopes needed for the demo, ideally profile and scorecard. Show the request ID in the backend.

Switch back to the student portal. Show the actual pending request. Accept it and explicitly grant only the scopes selected by the student. Show the expiry date.

Narration:
> “The recruiter asks. The student decides what to share and for how long.”

### Scene 5 — Approved reveal

Return to the organization portal and open the granted request. Show only fields permitted by the grant. If contact details or project files were not granted, show that they remain unavailable.

Show the audit event and expiry. If time permits, withdraw access from the student portal and demonstrate that subsequent access is denied.

Narration:
> “AptoHire does not make identity a default. Access is scoped, time-limited, revocable and enforced by the backend.”

## Pass/fail conditions

The demo is **PASS** only if all are true:

- The student portal uses Supabase, not the mock adapter.
- The project submission creates a real database row and queued agent run.
- A real provider request is evidenced by a fresh run record and provider usage/log.
- The scorecard is derived from the persisted audit result, not hard-coded sample data.
- The evidence refs exist in the server-created manifest.
- The organization portal uses the same Supabase project.
- The candidate card is blind before consent.
- The request is persisted by `request-access`.
- The student's response is persisted by the consent endpoint.
- `reveal-student` returns only the granted scopes.
- Withdrawal/expiry prevents subsequent reveal.
- No service-role key or provider key is present in browser bundles or frontend env files.

If any step is still mock, label the demo as a prototype walkthrough and do not call it an end-to-end backend integration.

## Capture an evidence bundle for judges

Create a `demo-evidence/` folder with:
- `01-submission-created.png`
- `02-agent-run-provider-provenance.png`
- `03-evidence-manifest-and-audit.png`
- `04-student-scorecard.png`
- `05-blind-candidate-card.png`
- `06-access-request.png`
- `07-student-consent.png`
- `08-scoped-reveal.png`
- `09-withdrawal-denied.png` (if demonstrated)
- `demo-run-metadata.json` with secrets and personal data removed

Do not include API keys, auth tokens, raw student IDs, private evidence files or real contact details in screenshots.
