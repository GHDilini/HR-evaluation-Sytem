# DECISIONS.md

Decisions made for this project and why. Read this before changing an existing approach, so settled choices aren't re-debated or undone.

## How to use this file

- Add a row for every decision made during implementation, with the task ID.
- Never delete a row. To change a decision, add a new row that supersedes the old one and mark the old row "Superseded by D-xx".
- Keep each row short: the decision, the reason, and where it's documented.

## Decisions

| ID | Date | Decision | Why | Source | Status |
| --- | --- | --- | --- | --- | --- |
| D-01 | 2026-11-11 | Build a custom internal tool; no product comparison or pricing work | Internal HR tool; product owner decision | PRD | Active |
| D-02 | 2026-11-11 | MVP runs on localhost with made-up data and a mock HRIS API; real HRIS integration comes before the pilot | Build without real data or HRIS access | PRD, ARCHITECTURE | Active |
| D-03 | 2026-11-11 | Next.js with TypeScript, Supabase, Tailwind CSS and shadcn/ui, Playwright and GitHub Actions | One codebase; database, login and files from one tool | ARCHITECTURE, RULES | Active |
| D-04 | 2026-11-11 | Data tables live in a private `app` schema; the web app calls only `api` functions | The automatic API can't reach tables, so direct reads are impossible | DESIGN | Active |
| D-05 | 2026-11-11 | Elevated functions are owned by the `app_owner` role, with a policy on every forced-RLS table | Forced access rules apply to table owners too | DESIGN | Active |
| D-06 | 2026-11-11 | Only `app.move_stage` changes a form's stage, following the Stage moves table | One place enforces the workflow | DESIGN | Active |
| D-07 | 2026-11-11 | Employee and lead submit independently in either order; answers reveal when both have submitted | Prevents one evaluation biasing the other | PRD | Active |
| D-08 | 2026-11-11 | Locked ratings never change; mistakes get dated correction notes; no appeal route in the system | Product owner decision | PRD | Active |
| D-09 | 2026-11-11 | Answers are stored as JSON per side, in the formats in DESIGN.md | Flexible form; validated against the template version | DESIGN | Active; revisit if cross-form reports are needed |
| D-10 | 2026-11-11 | Client PDFs stream only through `/files/pdf/[id]`; no shareable links | A copied address must fail for anyone without access | PRD P5, DESIGN | Active |
| D-11 | 2026-11-11 | Scheduled jobs use pg_cron, calling app endpoints through pg_net with an `x-job-secret` header | Works locally and hosted; secret kept in Supabase Vault | DESIGN | Active |
| D-12 | 2026-11-11 | Login lockout runs in Supabase's password verification hook | An app-only lockout can be bypassed | DESIGN | Active; check plan support in T-2.4 |
| D-13 | 2026-11-11 | The most senior person is excluded from cycles in the MVP | No lead to evaluate them; evaluator option comes in the pilot | PRD A1 | Active |
| D-14 | 2026-11-11 | A made-up holiday list is seeded for the MVP; HR enters holidays from the pilot | Working-day rules need holidays from day one | PRD, DESIGN | Active |
| D-15 | 2026-11-11 | Brand colours: royal blue for actions, light orange for highlights, on white; light and dark mode | Product owner decision | DESIGN | Active |
| D-16 | 2026-11-11 | No HR approval is needed for the documents; the form, scale and microcopy are final | Product owner decision | PRD, DESIGN | Active |
| D-17 | 2026-11-11 | Sri Lanka time, English only; Sri Lanka's Personal Data Protection Act applies | Product owner decision | PRD | Active |
| D-18 | 2026-11-11 | One branch per task, named by task ID; Conventional Commits | Clean rollback point for every task | RULES | Active |
