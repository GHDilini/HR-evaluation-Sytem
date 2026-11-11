# TASKS.md — Performance Evaluation System

## How to maintain this file

- Before starting a task, mark it `[~]` and confirm its dependencies are done.
- After completing a task, mark it `[x]` only when all acceptance criteria are met and tests pass.
- If blocked, mark it `[!]` and write the reason underneath.
- Update the Progress Summary table and add a Change Log entry after every status change.
- Never silently add, remove or rescope tasks. Propose changes and add them to the Change Log with a reason.
- Work on one task at a time unless told otherwise.

## 1. Project Overview

**Project:** Performance Evaluation System

**Goal:** Replace evaluation spreadsheets with one web app where employees and leads evaluate independently, agree final ratings that lock permanently, and read history and KPIs by year.

**Current status:** Planning complete; the local repository holds the documents and agent setup files; no code yet. Phases 0 to 9 deliver the localhost MVP and staging for HR; phases 10 and 11 cover the pilot and rollout.

### Progress Summary

| Phase | Total Tasks | Done | In Progress | Blocked | % Complete |
| --- | --- | --- | --- | --- | --- |
| 0. Project setup | 7 | 0 | 1 | 0 | 0% |
| 1. Foundation | 13 | 0 | 0 | 0 | 0% |
| 2. Employee data and accounts | 7 | 0 | 0 | 0 | 0% |
| 3. Cycles, KPIs and blind evaluation | 9 | 0 | 0 | 0 | 0% |
| 4. Final ratings and disputes | 5 | 0 | 0 | 0 | 0% |
| 5. History | 2 | 0 | 0 | 0 | 0% |
| 6. HR tools | 4 | 0 | 0 | 0 | 0% |
| 7. Scheduler and notifications | 3 | 0 | 0 | 0 | 0% |
| 8. Client PDFs | 3 | 0 | 0 | 0 | 0% |
| 9. Quality and staging | 6 | 0 | 0 | 0 | 0% |
| 10. Pilot | 13 | 0 | 0 | 0 | 0% |
| 11. Rollout | 3 | 0 | 0 | 0 | 0% |
| **Total** | **75** | **0** | **1** | **0** | **0%** |

## 2. Status Legend

- `[ ]` Not started
- `[~]` In progress
- `[x]` Done
- `[!]` Blocked (with reason)

Each task lists **Priority** (High / Medium / Low), **Size** (S / M / L) and **Depends on**. Requirement IDs such as E4 come from PRD.md; section names refer to DESIGN.md unless marked otherwise.

## 3. Phases and Tasks

### Phase 0: Project setup

**Goal:** A runnable empty app, local Supabase, mock HRIS and CI on every pull request.

**Definition of Done:** `npm run dev:all` starts everything; CI runs on a pull request and passes on the empty app; `main` is protected; pg_cron and pg_net are confirmed.

#### [ ] T-0.1 Install the developer toolchain
- **Priority:** High · **Size:** S · **Depends on:** none
- **Description:** Install Node.js 26 LTS, Git, a container tool and the Supabase CLI 3.x on the development Mac.
- **References:** RULES.md §3; ARCHITECTURE.md Developer handover
- **Files/areas:** None (machine setup)
- **Acceptance criteria:**
  - [ ] `node -v`, `npm -v`, `git --version` and `supabase --version` all succeed
  - [ ] `supabase start` runs in an empty test folder

#### [ ] T-0.2 Create the repository and app skeleton
- **Priority:** High · **Size:** M · **Depends on:** T-0.1
- **Description:** Create the private GitHub repository and a Next.js 16 app in strict TypeScript, with ESLint, Prettier, Vitest, Playwright and the folder map from RULES.md §4.
- **References:** RULES.md §3, §4, §5; DESIGN.md Code structure
- **Files/areas:** `app/`, `components/`, `features/`, `lib/`, `types/`, `tests/`, `package.json`
- **Acceptance criteria:**
  - [ ] `npm run build`, `npm run lint`, `npm run typecheck` and `npm run test:unit` pass
  - [ ] Exact versions are pinned and the lockfile is committed
  - [ ] Every script name from RULES.md §5 exists
  - [ ] TypeScript 7 works with Next.js 16 and ESLint, or the fallback version is recorded in the Change Log
  - [ ] A sample Playwright test runs

#### [ ] T-0.3 Set up local Supabase
- **Priority:** High · **Size:** S · **Depends on:** T-0.2
- **Description:** Initialise Supabase in the repo, publish only the `api` schema to the automatic API, turn off sign-ups, and add `npm run db:reset`, the pgTAP test harness and a `.env.example` with every variable in DESIGN.md Configuration.
- **References:** Database access design; Login and sessions; Configuration
- **Files/areas:** `supabase/config.toml`, `supabase/migrations/`, `scripts/`
- **Acceptance criteria:**
  - [ ] `npm run db:reset` rebuilds an empty database
  - [ ] Only the `api` schema is listed as exposed in the configuration
  - [ ] Sign-ups are disabled
  - [ ] `npm run test:db` runs a sample pgTAP test
  - [ ] `.env.example` lists every variable in DESIGN.md Configuration

#### [ ] T-0.4 Create the mock HRIS service skeleton
- **Priority:** High · **Size:** M · **Depends on:** T-0.2
- **Description:** Build the Hono service on port 4000 with `GET /employees` and `GET /employees/{id}`, an API key check and 50 made-up employees using `example.test` emails.
- **References:** Mock HRIS API and sync; PRD HRIS integration
- **Files/areas:** `mock-hris/`
- **Acceptance criteria:**
  - [ ] Both endpoints return the employee fields listed in DESIGN.md
  - [ ] A missing or wrong key returns an error
  - [ ] Unit tests cover both endpoints

#### [ ] T-0.5 Add the start script and CI pipeline
- **Priority:** High · **Size:** M · **Depends on:** T-0.3, T-0.4
- **Description:** Add `npm run dev:all`, and a GitHub Actions workflow that builds, lints, type-checks, runs unit tests and rebuilds the database. Protect `main` and require pull requests.
- **References:** ARCHITECTURE.md DevOps and CI/CD; RULES.md §10
- **Files/areas:** `scripts/`, `.github/workflows/ci.yml`
- **Acceptance criteria:**
  - [ ] `npm run dev:all` starts Supabase, the mock and the app
  - [ ] CI runs on a pull request and blocks merging on failure
  - [ ] Direct pushes to `main` are refused

#### [~] T-0.6 Add project documents and agent rules
- **Priority:** Medium · **Size:** S · **Depends on:** T-0.2
- **Note:** Documents, CLAUDE.md, AGENTS.md, DECISIONS.md, `.env.example` and `.gitignore` are committed; the README remains.
- **Description:** Copy PRD.md, ARCHITECTURE.md, DESIGN.md, RULES.md and this file into the repo root, plus a `CLAUDE.md` pointing to RULES.md and a README with setup steps.
- **References:** RULES.md §1
- **Files/areas:** repo root, `README.md`, `CLAUDE.md`
- **Acceptance criteria:**
  - [x] All five documents are in the repo root
  - [ ] A new contributor can start the app by following the README

#### [ ] T-0.7 Confirm pg_cron and pg_net locally
- **Priority:** High · **Size:** S · **Depends on:** T-0.3
- **Description:** Enable both extensions locally, store `JOB_SECRET` and `APP_URL` in Supabase Vault, and prove a scheduled job can call an app endpoint with the secret header.
- **References:** DESIGN.md Open design questions
- **Files/areas:** `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] A test job runs on schedule and reaches the app
  - [ ] The result is recorded in the Change Log

### Phase 1: Foundation

**Goal:** The full database with access rules, stage control and audit, plus the design system and core components.

**Definition of Done:** Must-pass tests 1, 3, 4 and 5 pass at database level; the app shell shows tokens in light and dark mode; CI checks the master key rule.

#### [ ] T-1.1 Create people and access tables
- **Priority:** High · **Size:** S · **Depends on:** T-0.3
- **Description:** Create the `app`, `api` and `jobs` schemas, the `app_owner` role, the enums, and `people`, `user_roles`, `reporting_lines`, `invites` and `login_attempts` as in Schema details.
- **References:** Schema details; Data model
- **Files/areas:** `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] Tables, constraints and indexes match Schema details
  - [ ] Row-level security is on and forced, with an `app_owner` policy and no `anon` permissions
  - [ ] Signed-in users can't switch to `app_owner` (pgTAP test)

#### [ ] T-1.2 Create evaluation tables
- **Priority:** High · **Size:** M · **Depends on:** T-1.1
- **Description:** Create `template_versions`, `cycles`, `holidays`, `forms`, `kpis`, `evaluation_sides`, `final_results`, `form_returns`, `client_pdfs` and `correction_notes`.
- **References:** Schema details; PRD C3
- **Files/areas:** `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] Tables, checks and indexes match Schema details
  - [ ] A used template version refuses edits

#### [ ] T-1.3 Create system tables and time helpers
- **Priority:** High · **Size:** S · **Depends on:** T-1.1
- **Description:** Create `audit_events`, `email_outbox`, `sync_runs`, `sync_errors` and `test_clock`, plus `app.current_time()` and `app.add_working_days()` for Asia/Colombo.
- **References:** Schema details; Scheduler, time and notifications
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] `app.current_time()` returns the test clock value when set
  - [ ] Working-day counts skip weekends and holidays, with pgTAP tests

#### [ ] T-1.4 Add the audit log helper
- **Priority:** High · **Size:** S · **Depends on:** T-1.3
- **Description:** Add `app.log_event()` and a trigger that refuses every update and delete on `audit_events`.
- **References:** Audit events; PRD X9
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Events record actor, action, outcome, record, reason and time
  - [ ] Update and delete attempts fail, with a pgTAP test

#### [ ] T-1.5 Build stage control
- **Priority:** High · **Size:** M · **Depends on:** T-1.2, T-1.4
- **Description:** Add `app.move_stage()` with the Stage moves table, a trigger that refuses other stage changes and edits to final forms, and version checks.
- **References:** Form stages; Stage moves; Simultaneous actions; PRD F4
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Every allowed move succeeds and writes an audit event
  - [ ] Every move not in the table is refused (must-pass test 4)
  - [ ] Every write to a final-state form is refused (must-pass test 3)

#### [ ] T-1.6 Add row-level access rules
- **Priority:** High · **Size:** L · **Depends on:** T-1.5
- **Description:** Write access rules for every table by role and stage, so the other side stays hidden until the reveal.
- **References:** Access rules by role and stage; PRD E4, Permissions
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] pgTAP tests cover every row of the access rules table
  - [ ] Neither side can read the other before the reveal (must-pass test 1, database level)
  - [ ] HR can't use HR access on their own form

#### [ ] T-1.8 Seed the template and test users
- **Priority:** High · **Size:** S · **Depends on:** T-1.2
- **Description:** Seed the template in the exact JSON format from DESIGN.md Template and answer formats, the seven test logins and a made-up holiday list.
- **References:** Template and answer formats; Test users; PRD Time and language
- **Files/areas:** `supabase/seed/`
- **Acceptance criteria:**
  - [ ] `npm run db:reset` loads the template, every test login and the holidays
  - [ ] The template validates against the format in DESIGN.md
  - [ ] All seed data is made up and uses `example.test`

#### [ ] T-1.7 Prove tables are unreachable through the API
- **Priority:** High · **Size:** S · **Depends on:** T-1.6, T-1.8
- **Description:** Add API tests that try to read and change `app` tables through Supabase's automatic API as each role.
- **References:** Routes to the data; Abuse cases
- **Files/areas:** `tests/api/`
- **Acceptance criteria:**
  - [ ] Every attempt is refused (must-pass test 5)
  - [ ] The tests run in CI

#### [ ] T-1.9 Set up design tokens and themes
- **Priority:** High · **Size:** M · **Depends on:** T-0.2
- **Description:** Add every colour, type, spacing, radius and shadow token to Tailwind, with light and dark mode and a theme switch.
- **References:** Design system; RULES.md §3
- **Files/areas:** `app/globals.css`, `tailwind` config, `components/`
- **Acceptance criteria:**
  - [ ] Token values match DESIGN.md, including royal blue and light orange
  - [ ] An automated contrast check passes for every token pair in both modes

#### [ ] T-1.10 Add shadcn/ui primitives
- **Priority:** High · **Size:** M · **Depends on:** T-1.9
- **Description:** Add the shadcn/ui components from the component list, wired to the design tokens, with Sonner and Lucide.
- **References:** Components: Component list, Standard states
- **Files/areas:** `components/ui/`
- **Acceptance criteria:**
  - [ ] Each primitive supports the standard states in light and dark mode
  - [ ] Keyboard focus is always visible
#### [ ] T-1.11 Add the master key guard
- **Priority:** High · **Size:** S · **Depends on:** T-0.5
- **Description:** Create `lib/server/service-client.ts` and a CI check that fails if any file outside the master key list imports it.
- **References:** Master key use; RULES.md §9
- **Files/areas:** `lib/server/`, `scripts/`, `.github/workflows/`
- **Acceptance criteria:**
  - [ ] A test import from another file makes CI fail
  - [ ] The allowed file list matches DESIGN.md

#### [ ] T-1.12 Build the app shell
- **Priority:** High · **Size:** M · **Depends on:** T-1.10
- **Description:** Build the page layout, role-based sidebar, breadcrumb, phone menu and theme switch.
- **References:** User experience: Navigation; PRD A4
- **Files/areas:** `app/layout.tsx`, `components/`
- **Acceptance criteria:**
  - [ ] The sidebar shows only the areas a person's roles allow
  - [ ] The theme switch offers light, dark and device setting

#### [ ] T-1.13 Build the custom components
- **Priority:** High · **Size:** M · **Depends on:** T-1.12
- **Description:** Build the rating scale, stage chip, save status, section stepper and empty state.
- **References:** Components: Component list
- **Files/areas:** `components/`, `tests/unit/`
- **Acceptance criteria:**
  - [ ] Unit tests cover the rating scale and stage chip
  - [ ] Status is never shown by colour alone
### Phase 2: Employee data and accounts

**Goal:** Employees arrive from the mock HRIS, and everyone signs in with an invite.

**Definition of Done:** A sync loads 50 people with leads; each test user signs in and sees only their own area.

#### [ ] T-2.1 Add mock HRIS scenarios
- **Priority:** High · **Size:** M · **Depends on:** T-0.4
- **Description:** Add `POST /_scenario` and every scenario from DESIGN.md, including invalid records, a timeout and a wrong key.
- **References:** Mock HRIS API and sync: Scenarios
- **Files/areas:** `mock-hris/`
- **Acceptance criteria:**
  - [ ] Each scenario can be switched on and returns the expected data or failure
  - [ ] Scenario unit tests pass

#### [ ] T-2.2 Build the employee sync
- **Priority:** High · **Size:** M · **Depends on:** T-1.1, T-1.4, T-2.1
- **Description:** Build `jobs.sync_employees` and the `/api/jobs/sync` endpoint that fetches, checks with Zod and records sync runs and errors.
- **References:** Sync steps; PRD A1, S1 to S3, S9, S10
- **Files/areas:** `supabase/migrations/`, `app/api/jobs/sync/`, `lib/`
- **Acceptance criteria:**
  - [ ] The normal scenario creates 50 people with correct reporting lines
  - [ ] Invalid records are rejected and listed; a timeout keeps existing data (must-pass test 13)
  - [ ] People are matched by `employee_id`
  - [ ] Requests without the right `x-job-secret` header get `NOT_FOUND`

#### [ ] T-2.3 Add manual and daily sync
- **Priority:** Medium · **Size:** S · **Depends on:** T-2.2, T-0.7
- **Description:** Add `hr_run_sync` and a pg_cron job at 02:00 Sri Lanka time (20:30 UTC), refusing a second run with `SYNC_RUNNING`.
- **References:** Jobs and failures; PRD S1
- **Files/areas:** `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] HR can start a sync, and a second start is refused
  - [ ] The daily job is scheduled at `30 20 * * *`

#### [ ] T-2.4 Build sign-in and password pages
- **Priority:** High · **Size:** M · **Depends on:** T-1.13, T-2.2
- **Description:** Build `/login` and `/auth/set-password`, with lockout after 5 failures in 15 minutes enforced through Supabase's password verification hook, and identical messages for unknown emails.
- **References:** Login and sessions; PRD A2, A3
- **Files/areas:** `app/login/`, `app/auth/`, `features/auth/`
- **Acceptance criteria:**
  - [ ] Passwords under 12 characters are refused
  - [ ] Lockout and message checks pass (must-pass test 12)
  - [ ] Direct calls to Supabase's sign-in are also locked out, or the fallback is recorded in the Change Log
  - [ ] Screen states exist for both pages

#### [ ] T-2.5 Add sessions and suspension checks
- **Priority:** High · **Size:** S · **Depends on:** T-2.4
- **Description:** Add the 30-minute idle timeout with a 2-minute warning, and a check in every `api` function that the caller is active and not suspended.
- **References:** Login and sessions; PRD quality requirements
- **Files/areas:** `lib/`, `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] An idle session ends after 30 minutes, after a warning
  - [ ] A terminated or suspended person is refused on their next request

#### [ ] T-2.6 Build invites
- **Priority:** High · **Size:** S · **Depends on:** T-2.4
- **Description:** Build `hr_send_invite` with 7-day links that a new invite replaces, and invite status on the HR people list.
- **References:** Function contracts; PRD A5
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] Status shows not sent, sent, accepted or expired
  - [ ] An expired invite can be resent, and the old link stops working

#### [ ] T-2.7 Build roles and the administrator page
- **Priority:** Medium · **Size:** S · **Depends on:** T-2.4
- **Description:** Build `admin_set_hr_role` and `/admin`, refusing removal of the last HR person.
- **References:** Database functions; PRD Users
- **Files/areas:** `supabase/migrations/`, `app/admin/`
- **Acceptance criteria:**
  - [ ] The administrator can grant and remove the HR role, with audit events
  - [ ] Removing the last HR person is refused

### Phase 3: Cycles, KPIs and blind evaluation

**Goal:** HR opens a cycle, leads publish KPIs, and both sides evaluate blind until the reveal.

**Definition of Done:** Two test users complete and submit a form, and it reveals once, with all E-requirement tests passing.

#### [ ] T-3.1 Build cycle opening
- **Priority:** High · **Size:** M · **Depends on:** T-1.8, T-2.2
- **Description:** Build `hr_open_cycle`, which creates a cycle and one form per selected employee using the latest valid HRIS data.
- **References:** Function contracts; PRD C1, C3
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Deadlines must be in order, or the cycle is refused
  - [ ] Each form is linked to the cycle's template version
  - [ ] People without a lead are skipped and listed

#### [ ] T-3.2 Build the new cycle screen
- **Priority:** High · **Size:** M · **Depends on:** T-3.1, T-1.13
- **Description:** Build the 3-step wizard at `/hr/cycles/new` for details, deadlines and people.
- **References:** Screens and routes; User experience: Main tasks
- **Files/areas:** `app/hr/cycles/new/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] Inline errors and a success message match the microcopy
  - [ ] All four screen states exist

#### [ ] T-3.3 Build KPI publishing
- **Priority:** High · **Size:** S · **Depends on:** T-3.1, T-1.5
- **Description:** Build `publish_kpis` for 3 to 6 KPIs, editable until the first submission, moving KPI setup to Blind.
- **References:** Function contracts; PRD C4, C5
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Fewer than 3 or more than 6 KPIs are refused
  - [ ] Edits after the first submission are refused (must-pass test 11)

#### [ ] T-3.4 Build the KPI screen
- **Priority:** High · **Size:** S · **Depends on:** T-3.3, T-1.13
- **Description:** Build `/forms/[id]/kpis` for leads to add, edit and publish KPIs.
- **References:** Screens and routes; Screen states
- **Files/areas:** `app/forms/[id]/kpis/`, `features/kpis/`
- **Acceptance criteria:**
  - [ ] The employee is notified when KPIs are published
  - [ ] All four screen states exist

#### [ ] T-3.5 Build drafts, submission and the reveal
- **Priority:** High · **Size:** L · **Depends on:** T-3.3, T-1.6
- **Description:** Build `get_form`, `save_draft` and `submit_side`, returning only what the caller may see and revealing exactly once.
- **References:** Function contracts; Simultaneous actions; PRD E1 to E6
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`, `tests/api/`
- **Acceptance criteria:**
  - [ ] The other side is absent before the reveal (must-pass test 1)
  - [ ] Simultaneous submits create one reveal (must-pass test 2)
  - [ ] An old version number returns `CHANGED_ELSEWHERE` (must-pass test 14)

#### [ ] T-3.6 Build My tasks
- **Priority:** High · **Size:** S · **Depends on:** T-3.5, T-1.13
- **Description:** Build `get_my_tasks` and `/tasks`, sorted by due date.
- **References:** Screens and routes; PRD Screens
- **Files/areas:** `supabase/migrations/`, `app/tasks/`
- **Acceptance criteria:**
  - [ ] Each role sees only its own pending actions
  - [ ] The empty state says nothing needs action

#### [ ] T-3.7 Build the evaluation form screen
- **Priority:** High · **Size:** M · **Depends on:** T-3.5, T-1.13
- **Description:** Build `/forms/[id]` with sections, the section stepper, autosave and inline validation for the caller's own side.
- **References:** User experience: Patterns; Template and answer formats; wireframe
- **Files/areas:** `app/forms/[id]/`, `features/forms/`
- **Acceptance criteria:**
  - [ ] Drafts autosave every 30 seconds and when leaving a field
  - [ ] Errors appear beside fields with a summary at the top
  - [ ] All four screen states exist
#### [ ] T-3.8 Build side reopening
- **Priority:** Medium · **Size:** S · **Depends on:** T-3.5
- **Description:** Build `hr_reopen_side` for use before the reveal only.
- **References:** HR controls (PRD X2)
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Reopening after the reveal returns `STAGE_NOT_ALLOWED`
  - [ ] Both people are notified, and an audit event is written

#### [ ] T-3.9 Build submission and comparison
- **Priority:** High · **Size:** M · **Depends on:** T-3.7
- **Description:** Add the submit dialog, the stage banner and the side-by-side comparison after the reveal, shown as tabs on phones.
- **References:** User experience: Patterns, Microcopy; Components: Comparison view
- **Files/areas:** `features/forms/`, `components/`
- **Acceptance criteria:**
  - [ ] Only actions in `allowed_actions` appear
  - [ ] Differences are marked by an icon and the words "Different rating"
  - [ ] Phones show the two sides as tabs
### Phase 4: Final ratings and disputes

**Goal:** Leads enter agreed ratings, employees confirm or return them, and disputes have a way to finish.

**Definition of Done:** A form can reach Locked and Closed without agreement, and locked forms refuse every change.

#### [ ] T-4.1 Build meeting and final ratings
- **Priority:** High · **Size:** M · **Depends on:** T-3.5
- **Description:** Build `record_meeting`, `save_final` and `send_for_confirmation`.
- **References:** Function contracts; PRD F1, F2
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] A future meeting date is refused
  - [ ] Incomplete final ratings can't be sent

#### [ ] T-4.2 Build confirm and return
- **Priority:** High · **Size:** M · **Depends on:** T-4.1
- **Description:** Build `confirm_final`, which locks the form, and `return_final`, which moves the form to In dispute on the second return.
- **References:** Stage moves; PRD F3, F4
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] A confirmed form refuses every change (must-pass test 3)
  - [ ] A second return moves the form to In dispute

#### [ ] T-4.3 Build the final ratings screen
- **Priority:** High · **Size:** M · **Depends on:** T-4.2, T-1.13
- **Description:** Build `/forms/[id]/final` for the lead's entry and the employee's confirm or return, including the permanent-action dialog and tick.
- **References:** User experience: Patterns, Microcopy
- **Files/areas:** `app/forms/[id]/final/`, `features/final-ratings/`
- **Acceptance criteria:**
  - [ ] Confirming requires the "I understand" tick
  - [ ] All four screen states exist

#### [ ] T-4.4 Build dispute handling
- **Priority:** High · **Size:** S · **Depends on:** T-4.2
- **Description:** Build `hr_resolve_dispute` and `hr_close_dispute`.
- **References:** HR controls; PRD X6, X10
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] Resolving returns the form to Final ratings
  - [ ] Closing stores the lead's ratings and the employee's comment, then locks

#### [ ] T-4.5 Build correction notes
- **Priority:** Medium · **Size:** S · **Depends on:** T-4.2
- **Description:** Build `hr_add_correction_note` for final forms only.
- **References:** PRD F5
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] Notes are refused on forms that aren't final
  - [ ] The employee and lead are notified, and original ratings are unchanged

### Phase 5: History

**Goal:** Final evaluations become read-only web pages by year.

**Definition of Done:** The right people see the right years, with KPIs, and nobody else can open them.

#### [ ] T-5.1 Build history data
- **Priority:** High · **Size:** M · **Depends on:** T-4.2
- **Description:** Build `get_history`, returning final forms by year with KPIs, ratings, comments, PDFs and notes.
- **References:** Function contracts; PRD H1 to H3, H5
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] Only the employee, current lead and HR can read it
  - [ ] Locked forms show their frozen details copy

#### [ ] T-5.2 Build the history screen
- **Priority:** High · **Size:** M · **Depends on:** T-5.1, T-1.13
- **Description:** Build `/people/[id]/history` with a year selector and the KPI view.
- **References:** Screens and routes; PRD H2, H3
- **Files/areas:** `app/people/[id]/history/`, `features/history/`
- **Acceptance criteria:**
  - [ ] Choosing a year shows that year's evaluation
  - [ ] The empty state appears for years without evaluations

### Phase 6: HR tools

**Goal:** HR can see progress, people and the audit log.

**Definition of Done:** HR can run and repair a made-up cycle from the dashboard without seeing hidden answers.

#### [ ] T-6.1 Build the HR dashboard
- **Priority:** High · **Size:** M · **Depends on:** T-3.5, T-1.13
- **Description:** Build `get_hr_dashboard` and `/hr`, with filters by team, stage and overdue.
- **References:** Function contracts; PRD R1
- **Files/areas:** `supabase/migrations/`, `app/hr/`
- **Acceptance criteria:**
  - [ ] No answers appear during the blind stage (must-pass test 6)
  - [ ] Filters and counts work

#### [ ] T-6.2 Build the HR people screen
- **Priority:** Medium · **Size:** S · **Depends on:** T-2.3, T-2.6
- **Description:** Build `/hr/people` with synced employees, invite status, sync results and the "Sync now" button.
- **References:** Screens and routes; Screen states
- **Files/areas:** `app/hr/people/`
- **Acceptance criteria:**
  - [ ] Sync failures show a banner with the reason
  - [ ] Invite actions work from the list

#### [ ] T-6.3 Build the audit log screen
- **Priority:** Medium · **Size:** S · **Depends on:** T-1.4, T-1.13, T-2.7
- **Description:** Build `hr_get_audit` and `/hr/audit`, newest first, 50 per page.
- **References:** Audit events; PRD X8
- **Files/areas:** `supabase/migrations/`, `app/hr/audit/`
- **Acceptance criteria:**
  - [ ] Only HR and the administrator can read it
  - [ ] Filters return matching events

#### [ ] T-6.4 Test HR own-record refusal
- **Priority:** High · **Size:** S · **Depends on:** T-4.4, T-3.8
- **Description:** Add tests that every HR function refuses when the caller is the form's employee, and that a second HR person can act instead.
- **References:** Permissions; Test users
- **Files/areas:** `tests/api/`
- **Acceptance criteria:**
  - [ ] Every HR function returns `NOT_FOUND` on the caller's own form (must-pass test 7)
  - [ ] `hr2@example.test` can act on that form

### Phase 7: Scheduler and notifications

**Goal:** Reminders, disputes and emails happen on time without anyone signed in.

**Definition of Done:** Time tests prove reminders and disputes; every email from the notifications table reaches the test inbox. (T-7.1 moved to T-0.7.)

#### [ ] T-7.2 Build the email outbox and templates
- **Priority:** High · **Size:** M · **Depends on:** T-1.3
- **Description:** Build `app.enqueue_email` and one template per row of the PRD's Notifications table, in the microcopy style.
- **References:** Email outbox; PRD Notifications, R2
- **Files/areas:** `supabase/migrations/`, `features/notifications/`
- **Acceptance criteria:**
  - [ ] No template contains answers, ratings or comments
  - [ ] Each stage move queues the right emails

#### [ ] T-7.3 Build email sending
- **Priority:** High · **Size:** S · **Depends on:** T-0.7, T-7.2
- **Description:** Build `/api/jobs/send-emails`, sending up to 50 emails a minute to Mailpit, with one retry after 10 minutes and bounce marking.
- **References:** Jobs and failures
- **Files/areas:** `app/api/jobs/send-emails/`
- **Acceptance criteria:**
  - [ ] Emails appear in the test inbox
  - [ ] Two senders never send the same email
  - [ ] Requests without the right `x-job-secret` header get `NOT_FOUND`

#### [ ] T-7.4 Build the deadline job
- **Priority:** High · **Size:** M · **Depends on:** T-7.2, T-4.2
- **Description:** Build `jobs.process_deadlines` for reminders, overdue alerts and the 10-working-day dispute rule, scheduled every 15 minutes.
- **References:** Scheduler, time and notifications; PRD F3, R2
- **Files/areas:** `supabase/migrations/`, `supabase/tests/`
- **Acceptance criteria:**
  - [ ] 10 working days without a reply moves the form to In dispute (must-pass test 8)
  - [ ] Reminders go only to people who haven't acted (must-pass test 9)

### Phase 8: Client PDFs

**Goal:** Client PDFs can be added and opened safely, following the blind rule.

**Definition of Done:** PDFs follow the access rules at every stage, and no shareable link exists.

#### [ ] T-8.1 Build PDF upload
- **Priority:** High · **Size:** M · **Depends on:** T-3.5, T-1.11
- **Description:** Create the private storage bucket and build `begin_pdf_upload`, the upload action and `register_client_pdf`.
- **References:** Function contracts; PRD P1 to P4
- **Files/areas:** `supabase/migrations/`, `features/client-pdfs/`
- **Acceptance criteria:**
  - [ ] Only PDFs up to 20 MB are accepted
  - [ ] Uploads are refused on final forms

#### [ ] T-8.2 Build the PDF file route
- **Priority:** High · **Size:** S · **Depends on:** T-8.1
- **Description:** Build `authorize_pdf_download` and `/files/pdf/[id]`, which streams the file after a permission check.
- **References:** Routes to the data; PRD P3, P5
- **Files/areas:** `app/files/pdf/[id]/`
- **Acceptance criteria:**
  - [ ] Another signed-in user and a signed-out visitor are refused and logged (must-pass test 10)
  - [ ] The employee can't open a PDF before the reveal

#### [ ] T-8.3 Build the PDF interface
- **Priority:** Medium · **Size:** S · **Depends on:** T-8.2, T-3.9
- **Description:** Add the file upload component and PDF list to the form screen.
- **References:** Components: File upload
- **Files/areas:** `features/client-pdfs/`, `components/`
- **Acceptance criteria:**
  - [ ] Empty, uploading, uploaded and error states work
  - [ ] PDFs added after the reveal appear at once

### Phase 9: Quality and staging

**Goal:** The MVP passes every check and runs on a hosted staging copy for HR.

**Definition of Done:** All 15 must-pass tests pass in CI; HR can run a made-up cycle on staging; Gate 1 checks are ready.

#### [ ] T-9.1 Add the main-path browser test
- **Priority:** High · **Size:** M · **Depends on:** T-5.2, T-6.1, T-7.4
- **Description:** Add a Playwright journey through a full made-up cycle for all three roles.
- **References:** Test design; ARCHITECTURE.md CI check 6
- **Files/areas:** `tests/e2e/`, `.github/workflows/`
- **Acceptance criteria:**
  - [ ] The journey runs from opening a cycle to the locked history page
  - [ ] It runs in CI and blocks merging on failure

#### [ ] T-9.2 Check accessibility
- **Priority:** High · **Size:** M · **Depends on:** T-9.1
- **Description:** Add automated accessibility checks and a keyboard-only journey in light and dark mode.
- **References:** Design system; PRD Accessibility
- **Files/areas:** `tests/e2e/`
- **Acceptance criteria:**
  - [ ] Keyboard-only completion and contrast checks pass (must-pass test 15)
  - [ ] No serious issues are reported on main screens

#### [ ] T-9.3 Audit screen states
- **Priority:** Medium · **Size:** S · **Depends on:** T-9.1
- **Description:** Check every screen against the Screen states table and fix any gaps.
- **References:** Screen states
- **Files/areas:** `app/`
- **Acceptance criteria:**
  - [ ] Each screen has loading, empty, error and success states
  - [ ] Error messages match the microcopy guide

#### [ ] T-9.4 Complete CI checks
- **Priority:** High · **Size:** S · **Depends on:** T-9.1
- **Description:** Add the requirement-ID coverage check, secret and dependency scans, and time-based rule tests to CI.
- **References:** ARCHITECTURE.md CI checks; RULES.md §7
- **Files/areas:** `.github/workflows/`, `scripts/`
- **Acceptance criteria:**
  - [ ] CI fails if any MVP requirement ID has no test
  - [ ] All eight CI checks run on every pull request

#### [ ] T-9.5 Set up staging with automatic delivery
- **Priority:** High · **Size:** M · **Depends on:** T-9.4
- **Description:** Choose the staging email service (open question 5), then create the hosted staging copy with made-up data and automatic delivery after each merge to `main`.
- **References:** ARCHITECTURE.md Environments, DevOps by phase
- **Files/areas:** `.github/workflows/`, hosting settings
- **Acceptance criteria:**
  - [ ] A merge to `main` updates staging automatically
  - [ ] The staging setup refuses to start if the test clock contains a value
  - [ ] The chosen email service is recorded in the Change Log

#### [ ] T-9.6 Prepare Gate 1
- **Priority:** High · **Size:** S · **Depends on:** T-9.5
- **Description:** Run security and backup checks on staging and prepare HR's unaided trial cycle.
- **References:** PRD Delivery, Gate 1
- **Files/areas:** `docs/`
- **Acceptance criteria:**
  - [ ] Security and backup check results are recorded
  - [ ] HR has staging logins for a made-up cycle

### Phase 10: Pilot

**Goal:** One real team completes a real cycle on hosted production.

**Definition of Done:** Gate 2 criteria in PRD.md are met.

#### [ ] T-10.1 Connect the real HRIS
- **Priority:** High · **Size:** L · **Depends on:** T-9.6
- **Description:** Replace the mock with the real HRIS API through configuration and field mapping.
- **References:** PRD HRIS integration, S10
- **Files/areas:** `lib/server/`, `app/api/jobs/sync/`
- **Acceptance criteria:**
  - [ ] Contract checks against the real API pass
  - [ ] Workflow code is unchanged

#### [ ] T-10.2 Add pilot sync rules
- **Priority:** High · **Size:** M · **Depends on:** T-10.1
- **Description:** Add leaver handling, lead-change approval, pause rules and the reporting-line review before opening a cycle.
- **References:** PRD S4 to S8, S11, S12
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] The old lead keeps access until HR approves a lead change
  - [ ] A cycle can't open until HR confirms reporting lines

#### [ ] T-10.3 Add HR exception controls
- **Priority:** High · **Size:** M · **Depends on:** T-9.6
- **Description:** Build deadline extension, exclusion and re-inclusion, adding to an open cycle, temporary leads, suspension and naming an evaluator for the most senior person.
- **References:** PRD X1, X3 to X5, X7
- **Files/areas:** `supabase/migrations/`, `app/hr/exceptions/`
- **Acceptance criteria:**
  - [ ] Each control needs a reason and writes an audit event
  - [ ] A form excluded before the reveal reveals nothing

#### [ ] T-10.4 Add template editing
- **Priority:** Medium · **Size:** M · **Depends on:** T-9.6
- **Description:** Let HR create new template versions by editing labels, rating meanings and competencies.
- **References:** PRD C2
- **Files/areas:** `features/hr/`
- **Acceptance criteria:**
  - [ ] Editing creates a new version and never changes forms already created

#### [ ] T-10.5 Add pilot record functions
- **Priority:** Medium · **Size:** M · **Depends on:** T-9.6
- **Description:** Build `remove_client_pdf`, `hr_export_record`, `hr_export_status` and `get_rating_trend`.
- **References:** PRD P6, H7, R3, H4
- **Files/areas:** `supabase/migrations/`, `features/`
- **Acceptance criteria:**
  - [ ] Removed PDFs are hidden, not deleted, and logged
  - [ ] Status exports contain no answers

#### [ ] T-10.6 Add two-step login and PDF scanning
- **Priority:** High · **Size:** M · **Depends on:** T-9.6
- **Description:** Require two-step login for HR and the administrator, and scan PDFs for malware.
- **References:** PRD quality requirements, P1
- **Files/areas:** `features/auth/`, `features/client-pdfs/`
- **Acceptance criteria:**
  - [ ] HR can't sign in without the second step
  - [ ] A test malware file is refused
  - [ ] The chosen scanning service is recorded in the Change Log

#### [ ] T-10.10 Build holiday management
- **Priority:** Medium · **Size:** S · **Depends on:** T-9.6
- **Description:** Let HR enter and edit company holidays used for working-day counts.
- **References:** PRD Time and language
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] HR can add and remove holidays, with audit events
  - [ ] Working-day counts use the entered holidays

#### [ ] T-10.11 Complete the legal and privacy review
- **Priority:** High · **Size:** M · **Depends on:** T-9.6
- **Description:** Review the system under Sri Lanka's Personal Data Protection Act, including hosting location, retention, outside services and the employee privacy notice.
- **References:** PRD Privacy; ARCHITECTURE.md Compliance
- **Files/areas:** `docs/`
- **Acceptance criteria:**
  - [ ] The review's decisions are recorded
  - [ ] The privacy notice is published to employees before real data is used

#### [ ] T-10.12 Run load and browser tests
- **Priority:** Medium · **Size:** M · **Depends on:** T-9.6
- **Description:** Test page speed with 50 active users and check the main journey in Chrome, Edge, Safari and Firefox.
- **References:** PRD Performance, Browsers
- **Files/areas:** `tests/e2e/`
- **Acceptance criteria:**
  - [ ] Pages load within 2 seconds with 50 active users
  - [ ] The main journey passes in all four browsers

#### [ ] T-10.13 Measure success baselines
- **Priority:** Low · **Size:** S · **Depends on:** T-9.6
- **Description:** Measure HR hours from the last cycle and prepare the end-of-cycle survey.
- **References:** PRD Success measures
- **Files/areas:** `docs/`
- **Acceptance criteria:**
  - [ ] The baseline is recorded before the pilot cycle
  - [ ] The survey is ready to send after the pilot cycle
#### [ ] T-10.7 Set up production
- **Priority:** High · **Size:** L · **Depends on:** T-10.6, T-10.11
- **Description:** Create production with approved releases, secret stores, monitoring, alerts and a tested restore, including PDF backups.
- **References:** ARCHITECTURE.md DevOps by phase; PRD Backups
- **Files/areas:** `.github/workflows/`, hosting settings
- **Acceptance criteria:**
  - [ ] Releases need a person's approval and a backup first
  - [ ] A restore test, including PDFs, succeeds
  - [ ] At most two named people can reach the production database, and the error-monitoring service is chosen and recorded

#### [ ] T-10.8 Complete the security review
- **Priority:** High · **Size:** M · **Depends on:** T-10.7
- **Description:** Have an independent reviewer check access rules, elevated functions and master key use before real data.
- **References:** ARCHITECTURE.md Self-review
- **Files/areas:** `docs/`
- **Acceptance criteria:**
  - [ ] Findings are recorded and High findings are fixed

#### [ ] T-10.9 Run the pilot cycle
- **Priority:** High · **Size:** M · **Depends on:** T-10.2, T-10.3, T-10.8, T-10.10, T-10.12, T-10.13
- **Description:** Run one real cycle with the pilot team, following the PRD's pilot failure plan.
- **References:** PRD Delivery, Gate 2
- **Files/areas:** None (operations)
- **Acceptance criteria:**
  - [ ] All non-excluded pilot forms are completed
  - [ ] Gate 2 is approved

### Phase 11: Rollout

**Goal:** All 50 employees use the system.

**Definition of Done:** Every employee completes evaluations in the system, and support is in place.

#### [ ] T-11.1 Build past evaluation import
- **Priority:** Medium · **Size:** M · **Depends on:** T-10.9
- **Description:** Build `hr_import_evaluation` with preview, original rating scale and the "Imported" label.
- **References:** PRD H6
- **Files/areas:** `supabase/migrations/`, `features/hr/`
- **Acceptance criteria:**
  - [ ] Imported pages lock on saving and show their original scale

#### [ ] T-11.2 Build approved record deletion
- **Priority:** Low · **Size:** S · **Depends on:** T-10.9
- **Description:** Build `admin_delete_record` for deletions that a retention rule or law requires.
- **References:** PRD Record deletion
- **Files/areas:** `supabase/migrations/`
- **Acceptance criteria:**
  - [ ] Deletion needs HR approval and writes an audit event

#### [ ] T-11.3 Roll out to all employees
- **Priority:** High · **Size:** M · **Depends on:** T-11.1
- **Description:** Open the first company-wide cycle and hand support to the named owner.
- **References:** PRD Delivery: Rollout
- **Files/areas:** None (operations)
- **Acceptance criteria:**
  - [ ] All 50 employees are invited and active
  - [ ] Support and monitoring alerts reach the owner

## 4. Task Sizing Rules

- Each task fits one focused agent session and produces a small, reviewable pull request.
- Backend functions, screens and tests are separate tasks when each can be verified alone.
- Each phase ends with a runnable, testable app.

## 5. Open Questions & Risks

### Open questions

| # | Question | Affects |
| --- | --- | --- |
| 1 | Resolved: the documents are now named PRD.md, ARCHITECTURE.md and DESIGN.md | T-0.6 |
| 2 | Do pg_cron and pg_net work in the local Supabase setup? | T-0.7 |
| 3 | Should answers stay as JSON per side, or move to one row per question for cross-form reports? | T-1.2 |
| 4 | Resolved: a made-up holiday list is seeded for the MVP, and HR enters holidays from the pilot | T-1.8, T-10.10 |
| 5 | Which email service does staging use for HR's trial? | T-9.5 |
| 6 | Who is the administrator, support owner and Gate 1 approver? | T-9.6, T-11.3 |
| 7 | Which HRIS does the company use, and is its API available? | T-10.1 |
| 8 | Is a company logo needed in the sidebar? | T-1.12 |
| 9 | Is Supabase's password verification hook available on the chosen hosted plan? | T-2.4 |
| 10 | Which malware-scanning and error-monitoring services will the pilot use? | T-10.6, T-10.7 |

### Risks

| Risk | Mitigation |
| --- | --- |
| AI-written access rules leak hidden answers | Database tests for every rule, API tests per role, independent review in T-10.8 |
| The MVP holds 40 of 59 requirements, which is large for one builder | Finish phases 0 to 5 first, then add the rest |
| Next.js caching shows one person's page to another | Mark per-user pages dynamic, and test with two users |
| Staging or production hosting outside Sri Lanka needs legal approval | Complete the legal review before T-10.7 |
| Node.js and npm aren't installed yet | T-0.1 comes first |
| TypeScript 7 may not yet work with Next.js 16 or ESLint plugins | Check in T-0.2, and fall back to the newest supported version |

## 6. Change Log

| Date | Task ID | Change | Notes |
| --- | --- | --- | --- |
| 2026-11-11 | All | Created TASKS.md | 68 tasks from PRD, ARCHITECTURE, DESIGN and RULES |
| 2026-11-11 | T-0.2, T-0.3 | Added Playwright, TypeScript check, pgTAP harness and `.env.example` | Readiness review: Phase 0 setup gaps |
| 2026-11-11 | T-7.1 → T-0.7 | Moved the pg_cron and pg_net check into Phase 0 | Readiness review: the daily sync needs it in Phase 2 |
| 2026-11-11 | T-1.10, T-1.12, T-1.13 | Split the app shell and components into three tasks | Readiness review: too large for one session |
| 2026-11-11 | T-3.7, T-3.9 | Split the form screen into two tasks | Readiness review: too large for one session |
| 2026-11-11 | T-1.7, T-3.6, T-6.1, T-6.3 | Added missing dependencies | Readiness review |
| 2026-11-11 | T-1.1, T-1.8, T-2.2, T-2.4, T-3.1, T-7.3 | Added the app_owner role, template format, holidays, job secret, lockout hook and senior-person rule | Readiness review blockers and fixes |
| 2026-11-11 | T-10.10 to T-10.13 | Added holiday management, legal review, load and browser tests, and success baselines | Readiness review: PRD items without tasks |
| 2026-11-11 | T-0.6 | Marked in progress | Local repository created with the documents, CLAUDE.md, AGENTS.md, DECISIONS.md, `.env.example` and `.gitignore`; README still to do |
| 2026-11-11 | T-0.2 | Scope note | The local Git repository now exists; T-0.2 still creates the GitHub repository and app skeleton |
