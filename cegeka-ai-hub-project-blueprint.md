# Cegeka AI Hub — Project Blueprint

## 1. Project overview

**Project:** Cegeka AI Hub  
**Purpose:** A central internal platform where Cegeka employees can discover AI tools, learn practical skills, explore relevant use cases, experiment with prompts and workflows, and share knowledge.

**Vision:** **Discover. Learn. Experiment. Share.**

The AI Hub should make AI approachable and useful in everyday work. It should help colleagues understand what is available, identify where AI can help, and apply it responsibly.

### Initial decisions and constraints

| Area | Decision |
|---|---|
| Audience | All Cegeka employees |
| Product approach | Start with a focused MVP and expand incrementally |
| Product ambition | Polished, production-quality internal web application |
| Hosting | Existing Cegeka Azure infrastructure, subject to approval |
| Authentication | Microsoft Entra ID / company account |
| Initial content owners | AI Coach team |
| Initial language | English; design for future multilingual support |
| Branding | Cegeka branding with a dedicated AI Hub identity |
| Builder's role | Combination of educator, builder, and connector |
| Delivery approach | Start as an experiment (Phase 0), then grow into the MVP if the pilot shows value |
| Content management at start | Content-as-code: Markdown files with front matter in the repository, imported into the database. The admin UI follows in a later phase |
| Technology direction | Angular frontend and ASP.NET Core API (see 1.1) |

Infrastructure choices and technology standards must be confirmed against Cegeka's approved architecture and platform guidelines before implementation.

### 1.1 Frontend framework decision

Angular is retained. React was considered.

| Factor | Angular | React |
|---|---|---|
| Builder/maintainer skills | Strongest skill of the builder and maintainer | Would need ramp-up |
| Built-in capabilities | Router, forms, HTTP, DI, i18n included | Requires choosing libraries (router, forms, data fetching, i18n) |
| Fit with ASP.NET Core enterprise stack | Common pairing; opinionated structure | Also common; more freedom, more decisions |
| Entra integration | MSAL Angular | MSAL React |

**Rationale:** For a solo builder who is strongest in Angular, switching frameworks adds risk without a clear benefit for a content-driven app. Revisit only if the Cegeka design system is available exclusively as React components or a React-based team takes over maintenance.

### 1.2 Goals and success metrics

Define measurable goals before the pilot so that the experiment can be evaluated objectively.

| Goal | Example metric | Example pilot target |
|---|---|---|
| Employees find useful AI content | % of feedback marked helpful | ≥ 70% helpful |
| Employees return | Pilot users who return within 2 weeks | ≥ 50% |
| Content stays current | Items past review date | 0 at pilot end |
| Discoverability | Searches that return at least one result | ≥ 80% |
| Qualitative value | Pilot survey: "The AI Hub helps me in my work" | Average ≥ 4/5 |

Targets are placeholders to be agreed with the AI Coach team. Collect only the data needed to measure them (see section 12).

### 1.3 Target users

| Persona | Need |
|---|---|
| AI beginner (any department) | Understand what is allowed and get started with simple, safe examples |
| Practitioner (business or technical) | Find prompts and use cases for their role, quickly |
| Developer / technical specialist | Find developer tools, advanced prompts, and workflows |
| Manager / team lead | Identify opportunities and approved tools for their team |
| AI Coach / content admin | Publish and maintain content with little effort |

---

## 2. Product principles

1. **Practical first:** Focus on concrete tasks and outcomes rather than AI theory alone.
2. **Accessible to everyone:** Provide useful entry points for beginners and experienced technical users.
3. **Responsible by design:** Make approved usage, data handling, and security guidance easy to find.
4. **Content-driven:** Make it straightforward for the AI Coach team to keep information current.
5. **Incremental:** Deliver a useful MVP before adding advanced AI features.
6. **Measurable:** Gather proportionate feedback and learn which content is useful.
7. **Extensible:** Keep the architecture ready for more languages, content types, and capabilities.

## 3. Core pillars

### Discover
Help employees discover AI tools, capabilities, and practical opportunities.

### Learn
Offer short guides, tutorials, learning paths, and responsible-use guidance.

### Experiment
Provide reusable prompts, examples, and workflows employees can adapt.

### Share
Create a place for ideas, success stories, feedback, and community knowledge. Some community features can follow after the MVP.

---

## 4. MVP scope

The MVP should provide a useful, searchable content platform with an employee-facing experience. Content is initially managed as code (4.2); the full administration UI follows in a later phase (4.3).

### 4.1 Employee-facing features

#### Home dashboard
- Clear introduction to the AI Hub.
- Featured content selected by administrators.
- Recently published or updated content.
- Shortcuts to tools, prompts, use cases, and guides.
- Search entry point.

#### AI Tools catalogue
- Browse tools available or relevant within Cegeka.
- Tool detail page with description, provider, access method, website link, and availability status.
- Clearly distinguish company-approved, restricted, experimental, and external tools.
- Show how to get access (license request, self-service, not available), cost/licensing model where relevant, and which data classifications may be used with the tool.
- Show a contact or owner for questions about the tool.
- Show the date the availability status was last verified.
- Avoid implying that a tool is approved unless that status has been verified.

#### Prompt Library
- Browse and search reusable prompts.
- Filter by category, role, difficulty, or tags.
- Prompt detail page with purpose, instructions, example input/output where available.
- Show which tool(s) the prompt is intended for.
- Mark placeholders the user must fill in (for example `{{code}}`, `{{audience}}`) so they are easy to spot.
- Copy prompt action.
- Include both technical and non-technical prompts.

#### Related content
- Detail pages link to related items (for example a use case links to its tools, prompts, and guides).

#### Use Cases catalogue
- Practical examples of how AI can help with work.
- Organize by department, role, task, or category.
- Explain the problem, suggested approach, expected benefit, and relevant tools.
- Treat estimated time savings as indicative unless measured.

#### Guides and Tutorials
- Short, actionable guides.
- Beginner-to-advanced difficulty.
- Estimated reading or completion time.
- Markdown-based content.

#### AI Guidelines
- Responsible AI guidance.
- Security and privacy reminders.
- Data classification and approved-tool guidance, aligned with official company policies.
- Links to authoritative internal policies where available.
- Guideline list and guideline detail pages.
- Clearly state that the official policy prevails if it differs from AI Hub content.

#### Search
- Search across published tools, prompts, use cases, guides, and guidelines.
- Filter by content type, category, and language.
- Search results must never expose drafts or archived content to ordinary employees.
- MVP implementation: database full-text search (SQL Server full-text or PostgreSQL `tsvector`), behind an `ISearchService` abstraction so that Azure AI Search or semantic search can be added later without changing the API.

#### Feedback
- Allow employees to mark content as helpful or not helpful.
- Optionally allow a short comment or report that content is outdated.
- Keep collection proportionate and transparent.
- Tell users that comments are visible to content admins and must not contain confidential data.

#### Help and contact
- Footer or page explaining who runs the AI Hub, how to suggest content, and where to ask questions (for example a Teams channel).

### 4.2 Content management in the MVP (content-as-code)

The admin UI is not needed to start. Initially:

- Content lives as Markdown files with YAML front matter in a `content/` folder in the repository (one file per content item and language).
- Front matter holds metadata: type, slug, status, categories, tags, owner, review date, featured flag, type-specific fields.
- A content import step (CLI command or startup job in non-production environments, pipeline step in production) validates the files and upserts them into the database.
- Validation runs in CI: required fields, unique slugs, valid categories, English translation present, safe Markdown.
- Changes are reviewed through pull requests, which gives versioning, review, and an audit trail for free.
- The database model stays the same as the later admin-UI model (section 8), so the switch to UI-based editing does not require a data migration.

Example:

```markdown
---
type: Prompt
slug: generate-unit-tests
status: Published
language: en
categories: [development]
tags: [testing, csharp]
owner: ai-coach-team
reviewAt: 2027-05-01
featured: true
difficulty: Beginner
intendedTools: [github-copilot]
---
# Generate unit tests

Write unit tests for the following code: {{code}}
```

### 4.3 Administration features (phase 2, after the pilot)

- Admin dashboard with content overview.
- Create and edit content.
- Save drafts.
- Preview content before publishing.
- Publish and unpublish content.
- Archive content instead of physically deleting it.
- Restore archived content.
- Upload and manage images used in content.
- Manage categories and tags.
- Assign a content owner and review date.
- Review and moderate feedback.
- View basic content audit history.
- Identify content that is due for review.
- Optional later: e-mail or Teams reminders to owners when content is due for review.

Until this phase is delivered, sections 4.2 (content-as-code) and the pull-request process cover these needs.

### 4.4 Explicitly out of scope for the MVP

Do not build these before the core content platform is working:

- AI chatbot or conversational assistant.
- AI-powered recommendations.
- Agent marketplace.
- Skill tracking or employee assessments.
- Full learning-management system.
- Automated translation.
- LLM integration or prompt execution against company data.
- Advanced analytics or adoption scoring.
- Administration UI (moved to phase 2, see 4.3).

### 4.5 Potential later features

- Events, workshops, and office hours.
- AI news and announcements.
- AI Champions directory and community features.
- Employee idea submission.
- Success stories and show-and-tell posts.
- Favorites and saved content.
- AI-powered semantic search.
- AI Opportunity Finder to help identify automation opportunities.
- Prompt improvement assistant.
- Workflow generator.
- Adoption and value dashboard.
- Microsoft Teams tab or app that embeds the AI Hub.

---

## 5. Suggested initial content

Populate the platform before launch so that it feels useful immediately.

Suggested starting volume:

| Content type | Initial target |
|---|---:|
| AI tools | 8–10 |
| Prompts | 20–30 |
| Use cases | 10 |
| Guides | 5–8 |
| Guidelines | 3–5 |

Possible first guides:

1. Introduction to Generative AI.
2. Writing effective prompts.
3. Responsible AI at work.
4. Getting started with Microsoft Copilot.
5. Understanding AI hallucinations.
6. Everyday productivity with AI.

The Prompt Library should contain examples for technical and non-technical roles.

### Content governance

- The AI Coach team owns content quality and decides what is published.
- Tool availability status (`CompanyApproved`, `Restricted`, …) may only be set based on a confirmed decision from the responsible team (for example IT, security, or procurement), with the source recorded.
- Use a short editorial style guide: tone, length, structure of guides and prompts, and how to mark indicative claims (such as time savings).
- Each content item has a named owner; when an owner leaves, ownership moves to the AI Coach team.

---

## 6. Recommended architecture

Use a **modular monolith** for the MVP. Avoid microservices until there is a clear operational or scaling reason.

### 6.1 High-level components

- **Frontend:** Angular single-page application.
- **Backend:** ASP.NET Core REST API.
- **Database:** Azure SQL or PostgreSQL, depending on Cegeka's approved standard.
- **ORM:** Entity Framework Core.
- **Authentication:** Microsoft Entra ID.
- **Frontend authentication:** MSAL Angular.
- **API authentication:** Microsoft.Identity.Web.
- **Hosting:** Approved Azure hosting. Two options:
  - *Simplest:* one Azure App Service hosting both the API and the built Angular files (same origin, no CORS).
  - *Split:* Azure Static Web Apps for the frontend and Azure App Service for the API.
- **File storage:** Azure Blob Storage for images and attachments (only needed once image upload exists; content-as-code images can live in the repository).
- **Search:** Database full-text search for the MVP; Azure AI Search as a later option.
- **API contract:** OpenAPI document generated by the API; TypeScript client/models generated from it for Angular.
- **Secrets:** Azure Key Vault, using managed identity to access Azure resources.
- **Monitoring:** Application Insights or the approved company monitoring solution, for both API and frontend errors.
- **Infrastructure as code:** Bicep or Terraform, depending on the Cegeka standard.
- **Environments:** Local and at least one shared test environment for the experiment; add acceptance/production before the wider pilot.
- **CI/CD:** Existing Cegeka pipeline and deployment standards.

### 6.2 Architectural principles

- API-first separation between frontend and backend.
- Content-driven design: content should be managed through data, not hardcoded into Angular components.
- Enforce authorization on the API; frontend route guards are not a security boundary.
- Use DTOs rather than exposing EF Core entities.
- Keep business logic in application/service layers.
- Keep database access behind repositories or focused data-access services where useful; do not add abstraction without a reason.
- Use migrations for database changes.
- No LLM integration is required for the MVP.
- Design the content model for multiple languages from the start.
- Design UI text for translation from the start (Angular i18n or a runtime translation library), not only content.
- Cache published content responses (for example output caching with invalidation on import/publish); the app is read-heavy.

All Azure services and deployment patterns are recommendations pending architecture and security approval. Get a rough monthly cost estimate for the chosen services before provisioning.

### 6.3 Suggested backend layers

A practical solution structure could be:

```text
src/
├── Cegeka.AiHub.Api/
│   ├── Controllers/
│   ├── Authorization/
│   ├── Middleware/
│   └── Program.cs
├── Cegeka.AiHub.Application/
│   ├── Content/
│   ├── ContentImport/
│   ├── Search/
│   ├── Feedback/
│   ├── Categories/
│   └── Common/
├── Cegeka.AiHub.Domain/
│   ├── Entities/
│   ├── Enums/
│   └── ValueObjects/
└── Cegeka.AiHub.Infrastructure/
    ├── Persistence/
    ├── Authentication/
    ├── Storage/
    └── Services/
```

Adjust naming and layering to existing Cegeka conventions if there are established templates. For a small experiment, starting with fewer projects (for example `Api` + `Infrastructure`, or one project with clear folders) is acceptable; split later when it helps.

Suggested test projects: `Cegeka.AiHub.UnitTests` and `Cegeka.AiHub.IntegrationTests` (using `WebApplicationFactory` and a real database via Testcontainers or a test database).

---

## 7. Authentication and authorization

### Authentication
- Use Microsoft Entra ID with company accounts.
- Angular obtains an access token using MSAL Angular.
- Angular sends the bearer token to the API.
- API validates token signature, issuer, audience, and expiry using supported Microsoft identity libraries.
- Restrict sign-in to the Cegeka tenant (single-tenant app registration); decide whether guest/external accounts are allowed.
- Consider a Backend-for-Frontend (BFF) pattern with secure, HTTP-only cookies instead of tokens in the browser; current OAuth guidance for browser apps prefers it. MSAL Angular with bearer tokens is acceptable for an internal app if approved by security.
- Provide a `/me` endpoint that returns the user's display name and roles so the UI can show/hide admin features.

### Roles

| Role | Permissions |
|---|---|
| Employee | Read published content and submit feedback |
| ContentAdmin | Create, edit, publish, archive, and manage content |

Use Entra app roles or approved group-based assignment. Confirm the preferred assignment model with the identity/platform team.

### Authorization requirements
- All employee content endpoints require authentication unless an explicit internal policy permits otherwise.
- Admin endpoints require the `ContentAdmin` role or an equivalent approved policy.
- Enforce roles server-side.
- Never rely only on Angular route guards.
- Do not expose drafts through employee endpoints.

---

## 8. Data model

Use a shared content model with type-specific detail tables. This provides common publishing, translation, categorization, and ownership behavior without putting every possible field into one oversized table.

### 8.1 Main entities

#### ContentItem
- `Id` — UUID / GUID, primary key.
- `ContentType` — enum: `Tool`, `Prompt`, `UseCase`, `Guide`, `Guideline`.
- `Slug` — unique URL-friendly identifier.
- `Status` — enum: `Draft`, `Published`, `Archived`.
- `IsFeatured` — boolean.
- `OwnerObjectId` — Entra user/object ID or approved owner reference.
- `OwnerDisplayName` — cached display name, so the app does not need Microsoft Graph calls to render owners. A team name (for example "AI Coach team") is allowed.
- `ReviewAt` — optional review date.
- `SourcePath` — nullable; the content file path when imported from the repository (content-as-code).
- `PublishedAt` — nullable UTC timestamp.
- `CreatedAt` — UTC timestamp.
- `UpdatedAt` — UTC timestamp.
- Concurrency token, such as SQL `rowversion` where supported.

#### ContentTranslation
- `Id`.
- `ContentItemId` — FK to `ContentItem`.
- `LanguageCode` — e.g. `en`, `nl`, `fr`.
- `Title`.
- `Summary`.
- `BodyMarkdown`.
- `PromptText` — nullable, for prompt content.
- `ExampleInput` — nullable.
- `ExampleOutput` — nullable.
- `UpdatedAt` — UTC timestamp, to detect translations that are older than the English source.
- Unique constraint on `(ContentItemId, LanguageCode)`.

Every published item must have an English translation in the initial release. When a requested language is missing, fall back to English and tell the UI which language was returned.

#### ToolDetail
- `ContentItemId` — PK and FK to `ContentItem`.
- `WebsiteUrl`.
- `Provider`.
- `AccessType` — e.g. `Web`, `Desktop`, `IDE`, `Integration`.
- `Availability` — e.g. `CompanyApproved`, `Restricted`, `Experimental`, `External`.
- `AvailabilityVerifiedAt` — date the status was last confirmed.
- `AvailabilitySource` — reference to the decision or policy that confirms the status.
- `AccessInstructions` — how to get access (Markdown, translatable; can live in `ContentTranslation.BodyMarkdown` or a dedicated translated field).
- `LicenseModel` — e.g. `Included`, `PerUserLicense`, `Free`, `Unknown`.
- `MaxDataClassification` — highest data classification allowed with this tool, using the company classification scheme.

#### PromptDetail
- `ContentItemId` — PK and FK.
- `Difficulty` — `Beginner`, `Intermediate`, `Advanced`.
- `IntendedRole` — optional.

#### UseCaseDetail
- `ContentItemId` — PK and FK.
- `Department`.
- `Difficulty`.
- `EstimatedTimeSaved` — optional and clearly indicative unless validated.

#### GuideDetail
- `ContentItemId` — PK and FK.
- `Difficulty`.
- `EstimatedMinutes`.

#### Category
- `Id`.
- `Slug`.
- `IsActive`.

#### CategoryTranslation
- `Id`.
- `CategoryId` — FK.
- `LanguageCode`.
- `Name`.

#### Tag
- `Id`.
- `Name`.
- `Slug`.

Tags are kept language-neutral (short technical keywords) in the MVP. If tags must be shown in multiple languages, add a `TagTranslation` table like `CategoryTranslation`.

#### ContentFeedback
- `Id`.
- `ContentItemId` — FK.
- `UserObjectId`.
- `FeedbackType` — e.g. `Helpful`, `NotHelpful`, `Outdated`, `Comment`.
- `Comment` — nullable, with a maximum length.
- `Status` — e.g. `New`, `Reviewed`, `Resolved`, `Hidden` (needed for moderation).
- `CreatedAt` — UTC timestamp.
- One helpful/not-helpful vote per user per content item (unique constraint or upsert); comments can be multiple.
- Define a retention period, after which `UserObjectId` is removed or the feedback is deleted.

### 8.2 Junction and supporting tables

- `ContentCategory`: many-to-many link between content and categories.
- `ContentTag`: many-to-many link between content and tags.
- `ContentRelation`: links between content items (`SourceId`, `TargetId`, `RelationType` such as `UsesTool`, `RelatedGuide`, `RelatedPrompt`). Used for related content and for "intended tools" of prompts.
- `SlugRedirect`: old slug → content item, so shared links keep working after a slug changes.
- Optional `ContentInteraction`: only if basic events such as content views or prompt copies are genuinely needed. Avoid collecting excessive usage data.
- `ContentAudit`: record important administrative changes, such as publish, unpublish, archive, and content edits.

### 8.3 Data storage guidance

- Store images and attachments in Blob Storage.
- Store file metadata and blob references in the database.
- Do not store arbitrary complete content objects as unvalidated JSON blobs.
- Use foreign keys and unique constraints.
- Use UTC timestamps.
- Add indexes for common filters such as status, content type, slug, and review date.
- Handle concurrent admin edits using optimistic concurrency.
- Sanitize Markdown output and do not allow unsafe arbitrary HTML or scripts. For example: render Markdown on the server with Markdig with raw HTML disabled, or render in Angular and sanitize (Angular's built-in sanitizer or DOMPurify). Pick one place and test it.

---

## 9. API design

Base route: `/api/v1`

Use REST conventions, DTOs, validation, pagination, and consistent error responses. Use Problem Details (RFC 9457) for API errors where appropriate.

### 9.1 Employee endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/home?language=en` | Aggregated homepage content |
| GET | `/tools` | List and filter tools |
| GET | `/tools/{slug}` | Tool detail |
| GET | `/prompts` | List and filter prompts |
| GET | `/prompts/{slug}` | Prompt detail |
| POST | `/prompts/{id}/copy` | Optional copy interaction tracking |
| GET | `/use-cases` | List and filter use cases |
| GET | `/use-cases/{slug}` | Use-case detail |
| GET | `/guides` | List guides |
| GET | `/guides/{slug}` | Guide detail |
| GET | `/guidelines` | List guidelines |
| GET | `/guidelines/{slug}` | Guideline detail |
| GET | `/search?q=...&type=...&language=en` | Search published content |
| GET | `/categories` | List active categories |
| GET | `/tags` | List tags in use |
| GET | `/me` | Current user's display name and roles |
| POST | `/feedback` | Submit feedback (rate limited) |

Only implement the prompt-copy endpoint if copy tracking is required. Copying a prompt can also be handled entirely in the frontend.

Detail endpoints that receive an old slug return a redirect (or the canonical slug) using `SlugRedirect`.

### 9.2 Admin endpoints (phase 2)

All endpoints below require the `ContentAdmin` authorization policy. During the content-as-code phase, only the import mechanism is needed; these endpoints are built together with the admin UI.

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/admin/content` | List and filter all content |
| GET | `/admin/content/{id}` | Get content for editing |
| POST | `/admin/content` | Create content |
| PUT | `/admin/content/{id}` | Update content |
| DELETE | `/admin/content/{id}` | Archive content; do not physically delete |
| POST | `/admin/content/{id}/publish` | Publish content |
| POST | `/admin/content/{id}/unpublish` | Return content to draft/unpublished state |
| POST | `/admin/content/{id}/restore` | Restore archived content to `Draft` |
| GET | `/admin/content/{id}/preview?language=en` | Render content as employees would see it |
| GET | `/admin/content/{id}/audit` | Audit history for one item |
| GET | `/admin/content/review-due` | Items past or near their review date |
| POST | `/admin/media` | Upload an image (validated type and size) |
| GET | `/admin/feedback` | List feedback |
| PUT | `/admin/feedback/{id}` | Moderate or update feedback status |
| GET | `/admin/categories` | List categories |
| POST | `/admin/categories` | Create category |
| PUT | `/admin/categories/{id}` | Update category |
| GET | `/admin/tags` | List tags |
| POST | `/admin/tags` | Create tag |
| PUT | `/admin/tags/{id}` | Update or merge tag |

Unpublishing moves the item back to `Draft`. Archiving is only possible from `Draft` or `Published`; restoring moves an archived item to `Draft`.

### 9.3 API conventions

- Pagination: `page`, `pageSize`.
- Sorting: allowlisted sort fields only.
- Filtering: explicit query parameters.
- Language: `language` query parameter initially; optionally support `Accept-Language`.
- Return DTOs, never EF entities.
- Validate all incoming data.
- Return useful validation errors.
- Use bearer-token authentication and policy-based authorization.
- Consider ETags or concurrency tokens for admin updates.
- Search only published content for employee requests.
- Publish an OpenAPI document and generate the Angular TypeScript client/models from it, so frontend and backend contracts cannot drift.
- Apply rate limiting to write endpoints such as feedback.
- Cap `pageSize` (for example 50).

Example list response:

```json
{
  "items": [
    {
      "id": "a8c4f19b-0000-0000-0000-000000000000",
      "slug": "generate-unit-tests",
      "title": "Generate unit tests",
      "summary": "Generate meaningful unit tests for existing code.",
      "difficulty": "Beginner",
      "category": {
        "name": "Software Development",
        "slug": "development"
      },
      "isFeatured": true
    }
  ],
  "page": 1,
  "pageSize": 12,
  "totalCount": 1
}
```

---

## 10. Angular application

### 10.1 Routes

```text
/
├── tools
│   └── :slug
├── prompts
│   └── :slug
├── use-cases
│   └── :slug
├── guides
│   └── :slug
├── guidelines
│   └── :slug
├── search
├── about
└── admin                (phase 2)
    ├── content
    ├── content/new
    ├── content/:id
    ├── categories
    ├── tags
    └── feedback
```

All employee routes require authentication. Admin routes additionally require the ContentAdmin role. Add a not-found page and an access-denied page.

### 10.2 Suggested folder structure

```text
src/app/
├── core/
│   ├── auth/
│   │   ├── auth.config.ts
│   │   ├── auth.guard.ts
│   │   └── auth.service.ts
│   ├── http/
│   │   ├── api.interceptor.ts
│   │   └── error.interceptor.ts
│   ├── layout/
│   │   ├── app-shell/
│   │   ├── header/
│   │   ├── sidebar/
│   │   └── footer/
│   └── services/
├── features/
│   ├── home/
│   ├── tools/
│   ├── prompts/
│   ├── use-cases/
│   ├── guides/
│   ├── guidelines/
│   ├── search/
│   └── admin/
│       ├── dashboard/
│       ├── content/
│       ├── categories/
│       └── feedback/
├── shared/
│   ├── components/
│   │   ├── content-card/
│   │   ├── search-bar/
│   │   ├── category-filter/
│   │   ├── difficulty-badge/
│   │   └── empty-state/
│   ├── models/
│   ├── pipes/
│   └── utilities/
├── app.routes.ts
└── app.config.ts
```

Feature folders can contain their own pages, components, services, models, and route configuration.

### 10.3 Frontend implementation guidance

- Use standalone components and lazy-loaded feature routes.
- Use the current supported Angular version; prefer the modern defaults (signals, built-in control flow, zoneless change detection where stable and supported by the libraries used).
- Use MSAL Angular for Entra authentication (or a BFF, see section 7).
- Use functional HTTP interceptors where suitable.
- Use Angular signals for local UI state and RxJS for asynchronous streams.
- Use reactive forms for admin content editing.
- Use route guards for user experience, while keeping authorization enforced by the API.
- Do not introduce NgRx unless state complexity demonstrates a real need.
- Build reusable content cards, filters, badges, search components, and empty states.
- Keep filters and search terms in the URL query string so results can be shared and bookmarked.
- Ensure responsive layouts and keyboard accessibility.
- Use the approved Cegeka design system where available; otherwise Angular CDK/Angular Material with a theme aligned with Cegeka branding.
- Use the generated API client (section 9.3) instead of handwritten HTTP models.

---

## 11. Content lifecycle and publishing

Suggested lifecycle:

```text
Draft → Published → Archived
```

A separate review state can be introduced later if the content approval process requires it.

Rules:

- Only published content is visible to employees.
- Drafts are visible only to authorized administrators.
- An English translation is required before publishing in the initial release.
- Every content item should have an owner.
- A review date should be set for content that can become outdated.
- Archiving should replace physical deletion for normal content management.
- Publishing, unpublishing, archiving, and significant edits should be auditable.
- Content updates should refresh `UpdatedAt`.
- Show administrators which items are overdue for review.

The admin area (phase 2) should support create, edit, preview, draft, publish, unpublish, archive, restore, categories, tags, ownership, review dates, and feedback moderation. In the content-as-code phase, the same lifecycle is expressed through the `status` front-matter field and pull requests; Git history serves as the audit trail.

---

## 12. Security and non-functional requirements

### Security

- Entra ID authentication.
- Server-side authorization and role checks.
- Validate token signature, issuer, audience, and expiry.
- HTTPS everywhere.
- Follow company data-classification and approved-tool policies.
- Store secrets in Key Vault or the approved secret-management solution.
- Validate API inputs and file uploads.
- Sanitize rendered Markdown/HTML.
- Restrict CORS to the actual frontend origin(s).
- Keep administrative audit records.
- Do not execute user prompts against company data in the MVP.
- Avoid collecting unnecessary personal or usage data.
- Enforce authorization independently of frontend navigation and visibility.
- Set security headers: Content Security Policy, `X-Content-Type-Options`, `Referrer-Policy`, HSTS.
- Rate-limit write endpoints.
- Keep dependencies up to date (Dependabot/Renovate or the company equivalent) and scan for vulnerable packages in CI.

### Privacy and compliance

- Feedback and interaction data are linked to employees and are personal data. Document what is collected, why, and for how long, and show a short privacy notice in the app.
- Check with the privacy officer/DPO whether a DPIA or record of processing is needed.
- Check whether collecting employee usage data requires consultation with employee representatives (works council) under local rules.
- Prefer aggregated or anonymous metrics where they are enough to measure the goals in section 1.2.
- Only use analytics cookies or telemetry that comply with company policy.

### Reliability and maintainability

- Structured logging and centralized monitoring.
- Health checks for the API and dependencies.
- Database migrations in the deployment process.
- Automated tests in CI.
- Document local development and deployment setup.
- Define backup, recovery, and retention expectations with the platform team.
- Use configuration per environment; do not hardcode secrets or environment-specific URLs.
- Define a support owner after the experiment: who fixes bugs, applies security updates, and renews the app registration secret/certificate (prefer managed identity and certificates over client secrets).
- Indicative performance target: pages and API list/search responses under 1 second at expected internal load.

### Accessibility and usability

- Target WCAG 2.2 level AA.
- Responsive desktop and mobile layouts.
- Keyboard navigation.
- Visible focus states.
- Semantic headings and controls.
- Sufficient contrast.
- Accessible form labels and validation messages.
- Clear loading, empty, and error states.

---

## 13. Testing strategy

Test the application at unit, integration, and end-to-end levels.

### Backend
- Domain and application service unit tests.
- API integration tests.
- Authentication and authorization tests.
- Validation and publishing-rule tests.
- Search and filtering tests.
- Content import validation tests (invalid front matter, duplicate slugs, missing English translation, unsafe Markdown).

### Frontend
- Angular component and service unit tests.
- Route and guard tests.
- Admin form validation tests.
- Accessibility checks (for example axe in end-to-end tests).
- End-to-end tests for important employee and admin flows (for example Playwright).

### Critical acceptance/security tests

1. Unauthenticated users cannot access protected endpoints.
2. An employee cannot call admin APIs successfully.
3. Draft content never appears in employee listings or search.
4. Publishing fails if the required English translation is missing.
5. Archived content is not available through normal employee detail routes.
6. Search returns only published content.
7. Invalid input is rejected with useful validation errors.
8. Concurrent admin edits are handled safely.
9. Markdown cannot execute arbitrary scripts.
10. File uploads, if enabled, are validated and restricted.
11. Content import rejects invalid files and never publishes content with `status: Draft`.
12. A user can register only one helpful/not-helpful vote per content item.

---

## 14. Development roadmap

The following estimate assumes one developer and reasonably timely access to approved infrastructure. It excludes approval delays, infrastructure provisioning delays, and the time needed to create and validate the initial content. Add a contingency of about 20–30%; first-time setup of Entra, pipelines, and Azure resources often takes longer than expected.

| Epic | Scope | Estimate |
|---|---|---:|
| 1. Project foundation | Angular and .NET setup, conventions, database/migrations, Entra auth, OpenAPI client generation, CI/CD | 4–5 days |
| 2. Application shell | Layout, responsive design, theme, UI i18n setup, route guards, reusable content cards | 2–3 days |
| 3. Content foundation | Entities, persistence, services, publishing rules, categories/tags, content-as-code import and validation | 4–6 days |
| 4. Employee experience | Home, catalogues, details, related content, search/filter, feedback | 5–7 days |
| 5. Pilot readiness | Tests, accessibility, security review, privacy notice, monitoring, responsive polish, pilot | 4–5 days |
| **MVP total (without admin UI)** |  | **19–26 developer days** |
| 6. Admin experience (phase 2) | Dashboard, content list, CRUD with Markdown editor and preview, publish/archive/restore, media upload, tags, feedback moderation, audit, review-due list | 6–9 days |

### Suggested milestones

**Milestone 0 — Experiment (walking skeleton)**
- Repository and application structure.
- Entra authentication against the Cegeka tenant.
- Application shell and navigation.
- Content-as-code import of a handful of Markdown items.
- Homepage, prompts list/detail with copy action, and tools list/detail.
- Deployed to one shared test environment and shown to a few colleagues.
- Decision point: continue to the MVP, adjust, or stop.

**Milestone 1 — Content platform**
- Full database model and migrations.
- Content API for all content types.
- Tools, prompts, use cases, guides, and guidelines.
- Search and filters.

**Milestone 2 — Pilot readiness**
- Feedback.
- Security, privacy, and accessibility checks.
- Monitoring and deployment.
- Initial content population (through content files and pull requests).
- Pilot with 10–20 employees across different departments, measured against section 1.2.

**Milestone 3 — Content administration (phase 2)**
- Admin authorization.
- Content CRUD and preview.
- Draft/publish/archive/restore workflow.
- Categories, tags, ownership, review dates, media upload.
- Migrate from content-as-code to UI-based editing (or keep both, with one clear source of truth per item).

A good first implementation step is Milestone 0: the application shell and homepage with company authentication and a few imported content items. This creates an early, visible result before the full content backend is complete.

---

## 15. Definition of Done for the MVP

The MVP is ready for pilot when:

- Employees can sign in with their Cegeka company account.
- Published tools, prompts, use cases, guides, and guidelines can be browsed.
- Content detail pages are available.
- Search and filtering work across published content.
- Prompts can be copied easily.
- Content can be added and updated through validated content files and pull requests.
- Content has an owner and review date.
- English is supported and the model is ready for additional languages.
- API authorization is enforced server-side.
- Critical automated tests pass.
- Monitoring and logging are configured.
- Security, privacy, and accessibility reviews have been completed to the agreed level.
- Initial content is populated and reviewed.
- Success metrics (section 1.2) are agreed and can be measured.
- A pilot group has tested the application and feedback has been addressed.

Phase 2 is done when authorized admins can create, edit, preview, publish, unpublish, archive, and restore content, and manage categories, tags, media, and feedback through the UI.

---

## 16. Risks, assumptions, and open questions

### Risks

| Risk | Mitigation |
|---|---|
| Single builder (bus factor of one) | Keep documentation, ADRs, and setup scripts current; keep the stack mainstream |
| Content goes stale after launch | Owners and review dates; review-due list; AI Coach team accountable |
| Showing a tool as approved when it is not | Status requires a recorded source and verification date |
| Approvals (architecture, security, identity, privacy) take longer than development | Request them early, in parallel with Milestone 0 |
| Overlap with existing intranet/SharePoint content | Link to authoritative sources instead of copying them |
| Experiment becomes production without hardening | Explicit decision point after Milestone 0 and pilot |

### Open questions

1. Which database is the Cegeka standard: Azure SQL or PostgreSQL?
2. Is there a Cegeka design system or Angular component library to use?
3. App roles or Entra groups for `ContentAdmin`? Who assigns them?
4. Are guest/external accounts allowed?
5. Which data-classification scheme should tool pages reference?
6. Who confirms tool availability status, and where is that recorded?
7. Is a DPIA or works-council consultation needed for feedback/usage data?
8. Which languages come after English, and when?
9. Who owns support and maintenance after the experiment?

Record decisions as short Architecture Decision Records (ADRs) in the repository, for example in `docs/adr/`.

---

## 17. Guidance for an AI coding assistant

When using this document as context in an IDE-based AI assistant, follow these instructions:

1. Treat this document as the product and architecture baseline, not as permission to make unapproved infrastructure or identity decisions.
2. First inspect the existing repository, solution structure, package versions, coding conventions, and any existing Cegeka templates before generating files.
3. Prefer existing project conventions and approved dependencies over introducing new frameworks or packages.
4. Do not generate the entire application in one step. Implement one milestone or vertical slice at a time.
5. Before making large structural changes, explain the proposed changes and identify assumptions.
6. Keep frontend and backend contracts aligned. Use explicit DTOs and validation.
7. Implement API authorization from the beginning; do not postpone it until after the UI.
8. Add tests alongside features, especially for publishing rules and authorization.
9. Do not invent Entra tenant IDs, client IDs, secrets, API URLs, subscription details, or company-specific configuration. Use clearly named configuration placeholders and document what an administrator must provide.
10. Do not claim that a tool, provider, or AI service is company-approved unless that status is explicitly confirmed.
11. Keep the MVP free of LLM integrations unless the scope is explicitly changed.
12. Maintain a concise implementation plan and update it as milestones are completed.
13. Ask focused questions only when a decision blocks safe or correct implementation. Otherwise, make conservative, clearly documented assumptions.
14. Prioritize a working, maintainable, secure vertical slice over speculative abstractions.
15. Do not build the admin UI before Milestone 3 unless the scope is explicitly changed; use content-as-code (section 4.2) instead.

### Recommended first task for the IDE assistant

If the repository is empty (greenfield), skip the inspection below. Instead, propose the solution layout, tooling versions, and the Milestone 0 plan, and wait for confirmation before scaffolding.

Otherwise, inspect the current repository and report:

- Existing frontend/backend structure and framework versions.
- Existing authentication and authorization setup.
- Existing database and migration approach.
- Existing UI component library and design system.
- Existing CI/CD and deployment conventions.
- Any conflicts between the current repository and this blueprint.

Do not modify files during this initial inspection. After reporting findings, propose the smallest first implementation milestone.
