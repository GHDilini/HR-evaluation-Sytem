# RULES.md — Performance Evaluation System

## 1. Purpose

This file sets the rules every contributor follows, whether an AI coding agent such as Claude Code or a human developer. Read it before every task.

The system is an internal web app for one company of about 50 employees in Sri Lanka. Employees and leads evaluate independently, see each other's answers only after both submit, agree final ratings that then lock permanently, and read past years as web pages. Stage: **MVP on localhost**, with made-up data and a mock HRIS API.

Sources of truth, in this order: `DESIGN.md`, `ARCHITECTURE.md`, `PRD.md`. If they conflict, stop and report it; don't choose yourself. Read `DECISIONS.md` before changing an existing approach, and record every new implementation decision there.

Two promises must never break:
- **Blind rule:** nobody sees the other side's answers before both have submitted (PRD E4).
- **Lock:** nothing changes a locked form, for any role, through any route (PRD F4).

## 2. General Principles

- Build the simplest thing that meets the requirement in `DESIGN.md`.
- Reuse existing components, functions and helpers; search before writing new ones.
- Keep each change small and focused on one build step or fix.
- Don't modify files unrelated to the task, even to tidy them.
- Use the exact names from `DESIGN.md` for tables, functions, stages, events and error codes. *Why: tests, docs and audit records depend on them.*
- Never guess rating meanings, deadlines, access rules, stage moves, wording or design tokens; ask.
- Use made-up data only, with `example.test` emails. Never use real employee data anywhere.

## 3. Technology & Code Standards

| Area | Rule |
| --- | --- |
| Runtime | Node.js 26 LTS |
| Language | TypeScript 7.x in strict mode, if T-0.2 confirms Next.js 16 and ESLint support it; otherwise the newest supported version, recorded in TASKS.md. No `any` without a comment explaining why |
| Framework | Next.js 16.x App Router, with React 19.x; server components by default |
| Database and login | Supabase CLI 3.x for local Supabase; `@supabase/supabase-js` 2.x and `@supabase/ssr` 0.12.x |
| Scheduled jobs | pg_cron, plus pg_net to call app endpoints |
| Styling | Tailwind CSS 4.x with the design tokens in `DESIGN.md`; no raw hex values or one-off sizes |
| UI components | shadcn/ui, Lucide icons and Sonner toasts; only components from the `DESIGN.md` list |
| Validation | Zod 4.x schemas, shared by browser and server |
| Mock HRIS | Hono 4.x in `mock-hris/`, port 4000 |
| Testing | Vitest 5.x for unit tests, pgTAP via `supabase test db`, Playwright 1.x for API and browser tests |
| Linting and formatting | ESLint 10.x and Prettier 3.x, run on every commit |
| Dependencies | Pin exact versions in `package.json` and commit the lockfile |
| File naming | kebab-case files (`stage-chip.tsx`); PascalCase components; snake_case for SQL |
| Migrations | `supabase/migrations/<timestamp>_<what>.sql`, one change per file |

## 4. Project Structure

```text
app/                 Next.js pages, layouts and server actions
app/api/jobs/        Protected job endpoints (send-emails, sync)
app/files/pdf/[id]/  The only route that streams client PDFs
components/ui/       shadcn/ui primitives, kept close to the originals
components/          Shared app components (rating scale, stage chip, comparison view)
features/<area>/     Feature code: forms, kpis, final-ratings, history, hr, admin
lib/server/          Server-only code; service-client.ts holds the master key
lib/                 Supabase helpers, Zod schemas, error messages, date helpers
types/               Shared TypeScript types and generated database types
supabase/migrations/ Database schema, access rules and functions
supabase/seed/       Made-up template, cycle and test logins
supabase/tests/      pgTAP database tests
mock-hris/           The mock HR data service and its scenarios
tests/unit/          Vitest unit tests
tests/api/           Function tests as each role
tests/e2e/           Playwright browser journeys
scripts/             Start, reset and test-data scripts
docs/                Extra notes; PRD, ARCHITECTURE, DESIGN, RULES, TASKS and DECISIONS stay in the root
```

- Put tables in the `app` schema, web-callable functions in `api` and job functions in `jobs`.
- Next.js code calls `api` functions only; it never reads `app` tables.
- Don't create new top-level folders without approval.

## 5. Commands

| Task | Command |
| --- | --- |
| Install | `npm ci` |
| Start everything | `npm run dev:all` (Supabase, mock HRIS and app) |
| App only | `npm run dev` |
| Reset database | `npm run db:reset` |
| Build | `npm run build` |
| Lint | `npm run lint` |
| Format check | `npm run format:check` |
| Type-check | `npm run typecheck` |
| Unit tests | `npm run test:unit` |
| Database tests | `npm run test:db` |
| Function tests | `npm run test:api` |
| Browser tests | `npm run test:e2e` |
| Everything CI runs | `npm run test:all` |

Create these scripts in build step 1 and keep their names.

## 6. Reference Patterns

Copy these patterns once they exist; until then, follow `DESIGN.md`:

- **Database function:** `supabase/migrations/<timestamp>_api_submit_side.sql`, which locks the row, checks the caller, moves the stage through `app.move_stage`, writes the audit event and returns `{ ok, data | error }`.
- **Server action:** `features/forms/actions/submit-side.ts`, which validates with Zod, calls one `api` function and maps error codes to microcopy.
- **Screen:** `app/forms/[id]/page.tsx`, a server component with loading, empty, error and success states.
- **Database test:** `supabase/tests/e4_blind_rule.test.sql`.

## 7. Testing Rules

- Add tests with every change to rules, functions or screens.
- Start each test name with its PRD requirement ID, such as `E4: employee cannot read lead side before reveal`.
- Test every `api` function for each role it must refuse, not only for success.
- Use the test clock (`app.current_time()`) and call job functions directly; never wait in real time.
- Use the fixed seed logins for each role; passwords live only in seed files.
- Keep the 15 must-pass tests in `DESIGN.md` passing at all times.
- Before saying a task is done, run `npm run lint`, `npm run typecheck` and the relevant tests, then report exactly what ran and the results.

## 8. Hard Boundaries (ask first)

Never do these without explicit approval:

- [ ] Add, remove or upgrade a dependency
- [ ] Change the database schema, access rules or an existing migration
- [ ] Edit `.env*` files, secrets or keys
- [ ] Change an `api` function's name, inputs or outputs
- [ ] Change stage moves, error codes, audit events or design tokens
- [ ] Modify CI/CD settings in `.github/workflows/`
- [ ] Delete, skip or weaken a test to make it pass
- [ ] Import the service key anywhere outside the master key list in `DESIGN.md`
- [ ] Create a new top-level folder

## 9. Security

- Validate every input with Zod on the server, and let the database check again.
- Build pages on the server from `api` results; hidden data must never reach the browser.
- Make every `api` function check the caller is active and allowed, and return `NOT_FOUND` when they can't see a record.
- Change a form's stage only through `app.move_stage`; lock the form's row and check its version before any change.
- Use elevated functions only for stage moves, audit records and jobs, always with `set search_path = ''`.
- Keep secrets in `.env.local` only; never log, print or commit them. Keep `.env.example` listing every variable in `DESIGN.md` Configuration.
- Accept job endpoint requests only with a matching `x-job-secret` header; return `NOT_FOUND` otherwise.
- Enforce login lockout in Supabase's password verification hook, so direct calls to Supabase can't skip it.
- Stream client PDFs only through `/files/pdf/[id]`; never create public or shareable links.
- Show comments and answers as plain text; never render them as HTML.
- Keep answers, ratings and comments out of logs, errors, emails and audit details.

## 10. Workflow

1. Read this file and the relevant `DESIGN.md` sections.
2. For anything bigger than a small fix, list the files you'll change and the tests you'll add, then wait for approval.
3. Work on one branch per task, named by its ID, such as `task/T-1.1-people-tables`; never commit to `main`.
4. Make one small change at a time, running tests as you go.
5. Ask when a requirement is unclear instead of guessing.
6. End each task with a summary: what changed, why, which requirement IDs it covers, and which checks ran. Update `TASKS.md`, and add any decision to `DECISIONS.md`.
7. Use Conventional Commits, such as `feat(forms): add submit_side (E2, E3, E5)` or `fix(rls): hide lead side before reveal (E4)`.
8. Merge only through a pull request with CI passing; access-rule changes need a person's review.

## 11. Known Gotchas

- **Master key:** the Supabase service key skips every access rule, so only the listed files may use it.
- **Time zone:** pg_cron runs in UTC, so 02:00 Sri Lanka time is `30 20 * * *` the day before.
- **Caching:** Next.js may cache pages; mark per-user pages as dynamic so one person's data is never shown to another.
- **Client components:** pass only the fields a client component needs, never whole records.
- **Sessions:** Supabase login tokens stay valid until they expire, so check suspension in every `api` function.
- **Backups:** database backups don't include stored files; back up PDFs separately before the pilot.
- **Light orange:** `#FFA94D` is too pale for text on white; use it only behind dark text or as a marker.
- **Answers:** answers are JSON keyed by the template's question IDs; validate them against the template version, not the latest template.
- **Forced access rules:** they apply to table owners too, so elevated functions work only through the `app_owner` policies; never switch the rules off instead.
- **Lockout bypass:** an app-only lockout can be skipped by calling Supabase's sign-in directly; keep it in the password verification hook.
- **Gotchas found during build:** _add new ones here_.

## Assumptions

- The source documents `PRD.md`, `ARCHITECTURE.md` and `DESIGN.md` sit in the repository root with this file, `TASKS.md` and `DECISIONS.md`.
- Vitest, ESLint and Prettier are added by this file, since the design names only pgTAP and Playwright.
- Versions are the latest on npm on 11 November 2026: Next.js 16.4, React 19.3, TypeScript 7.0, Tailwind CSS 4.3, Supabase CLI 3.1, Zod 4.6, Vitest 5.1, Playwright 1.65, Hono 4.13, ESLint 10.14 and Prettier 3.10.
- Node.js and npm aren't installed on this Mac yet; they're needed before build step 1.
- The folder layout, script names and reference file names are proposals until build step 1 creates them.
