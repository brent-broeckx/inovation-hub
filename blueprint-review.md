# Blueprint review — Cegeka AI Hub

Review of `cegeka-ai-hub-project-blueprint.md`. The changes below have been applied to the blueprint.

## Decisions taken

| Topic | Decision |
|---|---|
| Frontend framework | **Keep Angular.** The builder/maintainer is strongest in Angular; React would add risk without clear benefit (section 1.2). |
| Delivery | Start as an **experiment** (Milestone 0 with a go/adjust/stop decision). |
| Content management | **Git only.** Markdown with front matter, pull requests, CI validation. No admin UI, now or later. |
| Architecture | **Static Angular site, no backend API, no database.** Content is built into JSON and a search index at build time (section 1.1). |
| Feedback | **Contact an AI Champion.** The app stores no feedback or personal data. |
| Hosting/auth | **Experiment:** public GitHub Pages demo with sample content and guardrails (section 6.4). **After adoption:** Cegeka decides; Azure Static Web Apps with Entra login is recommended (Free plan + invitations for up to 25 pilot users, Standard for tenant-wide restriction). |

## What was removed

ASP.NET Core API, EF Core, Azure SQL/PostgreSQL, Blob Storage, MSAL, admin UI and admin endpoints, app roles, in-app feedback, audit tables. Git history and pull requests now cover versioning, review, and audit.

## What was added

1. Goals and success metrics based on surveys and champion input (no stored usage data).
2. Personas, including AI Champions and content contributors.
3. Content governance: tool approval needs a recorded source, style guide, champion consent, no confidential content.
4. Content model as a front-matter schema per content type, plus reference data (categories, champions).
5. Build output design, client-side search, slug redirects, language fallback.
6. CI content checks, preview environments per pull request, and an automated review-reminder pipeline.
7. Static Web Apps auth configuration example, security headers, and the rule that drafts must never reach the build output.
8. Risks and open questions (section 16), with ADRs for decisions.
9. Updated estimate: **15–21 developer days** plus 20–30% contingency.

## Open questions

See section 16 of the blueprint: hosting approval, GitHub vs Azure DevOps, guest access, design system, data-classification scheme, who confirms tool status, AI Champion list and consent, analytics, next languages, and support owner.

## Branding

All styling and visual assets use the Cegeka skills installed on the builder's machine (`cegeka-branding`, `cegeka-apps-style`, `cegeka-icons-use`, `cegeka-icons-illustrative-create`, `cegeka-illustrations-create`, `cegeka-illustrated-diagrams-create`, `cegeka-infographics-create`). See section 10.4 of the blueprint.
