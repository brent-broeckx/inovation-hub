# Blueprint review — Cegeka AI Hub

Review of `cegeka-ai-hub-project-blueprint.md`. The changes below have been applied to the blueprint.

## Overall

The blueprint is strong: clear MVP scope, sensible architecture (modular monolith, API-first, server-side authorization), and good security and testing basics. The main gaps were around **goals**, **experiment-first delivery**, **privacy**, **governance**, and a few **missing endpoints and data fields**.

## Decisions taken

| Topic | Decision |
|---|---|
| Frontend framework | **Keep Angular.** The builder/maintainer is strongest in Angular; React would add risk without clear benefit. Rationale added as section 1.1. |
| Delivery | Start as an **experiment** (new Milestone 0 with a go/stop decision point). |
| Admin UI | **Moved to phase 2.** Content starts as Markdown files with front matter in the repo, validated in CI and imported into the database (new section 4.2). The data model is unchanged, so no migration is needed later. |
| Scope | Otherwise unchanged. |

## What was missing (now added)

1. **Goals and success metrics** — the "Measurable" principle had no KPIs. Added section 1.2 with pilot targets.
2. **Personas** — added section 1.3.
3. **Content governance** — who may mark tools as approved, style guide, ownership when people leave.
4. **Privacy and compliance** — feedback stores user IDs: retention, privacy notice, DPIA, works-council check.
5. **Search approach** — now explicit: database full-text search behind an abstraction.
6. **Missing endpoints** — guideline detail, tags, `/me`, restore archived, preview, audit, review-due, media upload, tag admin.
7. **Data model gaps** — tool access instructions, licensing, max data classification, verification date/source; feedback status and one vote per user; content relations; slug redirects; translation fallback; owner display name.
8. **Risks and open questions** — new section 16, plus ADRs for decisions.
9. **Operations** — environments, infrastructure as code, cost estimate, support owner after the experiment, secret/certificate renewal.

## Improvements

- OpenAPI + generated TypeScript client to keep contracts aligned.
- Simpler hosting option: one App Service serving API + Angular (no CORS).
- BFF pattern noted as the more secure alternative to tokens in the browser.
- UI string translation, not only content translation.
- WCAG 2.2 AA target, security headers, rate limiting, dependency scanning.
- Modern Angular defaults (signals, built-in control flow, zoneless where stable), filters in the URL.
- Concrete Markdown sanitization options (Markdig without HTML, or DOMPurify).
- Estimates rebalanced: MVP without admin UI 19–26 days, admin UI 6–9 days, plus 20–30% contingency.
- AI-assistant guidance updated for a greenfield repo.

## Open questions for you / stakeholders

See section 16 of the blueprint: database standard, design system, role assignment, guest access, data-classification scheme, who confirms tool status, DPIA/works council, next languages, and long-term support owner.
