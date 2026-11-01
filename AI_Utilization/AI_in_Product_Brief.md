# AptoHire — AI in the Product

## 1. AI's Role

AptoHire uses AI to help students demonstrate what they can actually do—not just what they write on a resume. It analyses submitted projects and supporting evidence, builds an evidence-linked view of skills, and helps recruiters discover relevant candidates through skill-based matching.

AI supports assessment and discovery; it does not make the final hiring decision.

## 2. Core AI Capabilities

| Capability | What it does |
|---|---|
| **Project & Proof-of-Work Assessment** | Analyses project descriptions, code or work samples, demo videos, audio and supporting documents where applicable. |
| **Skill Extraction** | Identifies relevant technical, domain and communication skills demonstrated in the submitted evidence. |
| **Evidence-Linked Scorecard** | Produces a project-specific skill breakdown with scores and references to the evidence used. A score represents the submitted project/demo—not a universal measure of the student's ability. |
| **Guided Project Recommendations** | Helps students identify skill gaps and find suitable projects or learning tasks to build evidence. |
| **Living Skill Profile** | Consolidates assessed skills and student-declared skills into a continuously maintainable profile, keeping their evidence/status distinguishable. |
| **AI-Assisted Digital Resume** | Organises the student's profile into a digital resume. Connected profile information (for example, LinkedIn or GitHub) is used only with the student's permission and review. |
| **Blind Skill-Based Matching** | Compares job requirements with candidate skills and evidence, initially presenting suitable candidates without revealing identity. |

## 3. AI Assessment Workflow

1. A student submits a project title, description, demo video (for example, a three-minute walkthrough), and relevant files, links or code evidence.
2. AI processes the available inputs using suitable analysis components such as OCR/vision, audio analysis and code or text analysis.
3. The system extracts skills and connects each finding to the submitted evidence.
4. A project-specific scorecard is generated, showing skill-wise observations, scores and supporting evidence.
5. The result can contribute to the student's living profile. Students can raise a review ticket if they believe an assessment is incorrect.

**Important:** The scorecard should explain what evidence was assessed. Missing, inaccessible or insufficient evidence should be shown as such—not treated as proof of a missing skill. Human review is required for disputed or consequential assessments.

## 4. Proposed AI Agent Architecture

- **Proof-of-Work Audit Agent:** Coordinates analysis of project submissions and supporting evidence.
- **Vision & OCR Agent:** Extracts relevant information from screenshots, documents and visual project evidence.
- **Audio/Video Analysis Agent:** Analyses demo narration and visible demonstrations for relevant communication and task evidence.
- **Skill Extraction & Scorecard Agent:** Maps evidence to skills and produces an explainable, project-specific scorecard.
- **Guided Project Agent:** Suggests projects or tasks that may help students build evidence for skill gaps.
- **Profile Manager Agent:** Helps maintain the student's skill profile and prepare an AI-assisted digital resume.
- **Predictive Matching Agent:** Matches anonymised candidate skill profiles against recruiter job requirements.
- **Consent & Access Workflow:** Enforces the product's permission rules before identity or restricted profile information is revealed. This is a security/workflow responsibility, not a substitute for access-control enforcement in the backend.

These are proposed logical responsibilities; they may be implemented as separate agents or coordinated services.

## 5. Privacy, Fairness & Human Oversight

- Recruiters initially receive anonymised candidate matches.
- A student's identity and approved profile details are revealed only after the student grants access.
- Universities can access only the records and analytics belonging to their own institution.
- Students control profile integrations and should be able to review changes before they are applied.
- AI-generated scores must be explainable through evidence references and assessment criteria.
- Students need a way to challenge an assessment and request human review.
- AI matching should support recruiter decisions, not automatically reject or hire candidates.

## 6. MVP Scope vs. Future Scope

**MVP focus**
- Accept project submissions and supporting evidence.
- Extract skills from supported submission types.
- Generate evidence-linked, project-specific scorecards.
- Maintain a student skill profile.
- Match anonymised profiles to job requirements.
- Support student consent before recruiter access.

**Future enhancements**
- Broader multimodal assessment and additional file formats.
- More advanced guided learning and project recommendations.
- Career progression and alumni insights.
- Deeper integrations with external professional profiles and institutional systems.

The exact models, integrations, supported formats, scoring thresholds and assessment accuracy must be validated during implementation and pilot testing. Do not present proposed capabilities as live or validated until they have been tested.

## 7. Product Promise

**AptoHire turns project work into structured, evidence-backed skill signals—helping students show their capabilities and helping recruiters discover talent beyond the traditional resume.**
