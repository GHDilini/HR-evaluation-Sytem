# Performance Evaluation System — PRD

Nov 9, 2026 · updated Nov 27, 2026 to match the prototype · @Gayan

## Summary

Build a secure web app for 50 employees that replaces evaluation spreadsheets with one shared form per employee, per cycle. The lead first sets each team member's KPIs for the coming year. The employee then completes a self review and the lead a manager review, and neither sees the other's answers until both have submitted. HR schedules a meeting, the lead records the agreed final ratings during it, the system calculates an overall score, and HR confirms the review, which then locks permanently. Every completed evaluation becomes a web page in the employee's history, with a chart of KPI progress across years.

This version describes the clickable prototype in `docs/prototype/`. `DESIGN.md` and `ARCHITECTURE.md` still describe the earlier flow, where the employee confirmed the ratings, and need the same update before the build continues.

The work starts as a demo using made-up data, with employee details from a mock HRIS API. It moves to real HR use only after stakeholders approve it and it passes a pilot.

## Problem

The evaluation process runs on disconnected spreadsheets, so the company can't keep evaluations independent, agree outcomes traceably, or see performance over time.

| Pain today | Effect |
| --- | --- |
| Sheets are shared and stored separately | Nobody can be sure which version is current |
| Nothing hides one person's answers from the other | Seeing the other evaluation first can bias a rating |
| The two evaluations are combined by hand | Combining is slow, and the reasons for rating changes get lost |
| History is spread across old files | An employee's trend across years is hard to see |
| KPIs sit apart from the overall picture | KPI results are hard to find year by year |
| Client evaluations arrive separately as PDFs | Client feedback isn't kept with the evaluation it informs |

These pains come from the project owner's description of the current process. HR's time spent chasing late submissions is another likely pain.

## Goals

1. Keep every evaluation in one place, with one current version.
2. Guarantee that neither person sees the other's answers before both have submitted.
3. Record final ratings agreed in a meeting, calculate an overall score from them, and lock both once HR confirms.
4. Show each employee's evaluations by year as web pages, with the progress of their KPIs across years.
5. Keep each client evaluation PDF with the employee's evaluation.
6. Show HR who has and hasn't completed each stage.

## Success measures

These are the agreed targets. No baseline from the current process has been measured yet.

| Measure | Proposed target | When |
| --- | --- | --- |
| HR runs a full cycle without help, from opening it to viewing the locked history page | Achieved in one session | Demo |
| Reads of the other person's answers before the reveal | 0 in API tests and audit-log reviews | Demo, pilot and every release |
| Changes to locked ratings | 0 in API tests and audit-log reviews | Demo, pilot and every release |
| Evaluations completed in the system during the pilot | All non-excluded forms in the pilot team | Pilot |
| Evaluations handled through spreadsheets | 0 of 50 employees | Rollout |
| Non-excluded forms submitted by their original submission deadline | 90% | Pilot and rollout |
| HR hours spent per cycle chasing and combining evaluations | Lower than a baseline measured from the last cycle | Rollout |
| Users who agree the process was fair, in a short survey after the cycle | 80% | Pilot and rollout |

## Users

Four roles use the system; clients stay outside it and send PDFs. One person can be both an employee and a lead.

| User | Needs |
| --- | --- |
| Employee | Read the KPIs set for them, complete a private self review, see the lead's review after the reveal, attend the meeting, see the final ratings after it, read their own history and KPI progress |
| Lead | Set KPIs for each team member by a due date, review each team member privately, hold the meeting, enter the agreed final ratings during it, read their current team's history |
| HR | Run cycles, manage templates, send invites, review HRIS sync results, track completion, schedule each meeting, confirm each review with management, upload client PDFs, read evaluations from the reveal onward |
| Client (no account) | Send a PDF evaluation, as they do today |
| Administrator | Set up and remove HR users, handle approved deletions, and step in when an HR person's own form needs HR action |

An administrator is a named person at the company who sets up the first HR user and manages the HR role, with every change logged. The first administrator account is created during system setup, outside the invite process. Senior leaders have no separate role in the first version.

### User journeys

| User | Journey |
| --- | --- |
| Employee | Receive invite, set password, wait for the lead to publish KPIs, complete and submit the self review, read both reviews after the lead submits, get the meeting notice, attend the meeting, read the final ratings after it, read history |
| Lead | Set and publish KPIs for each team member, complete and submit each manager review after the employee submits, read both reviews, get the meeting notice, enter final ratings during the meeting and submit them, read team history |
| HR | Review HRIS data and reporting lines, open the cycle, send invites, track progress, set up the meeting once both reviews are in, discuss the final ratings with management and confirm the review, handle exceptions, upload client PDFs, read completed records |
| Administrator | Set up and remove HR users, and handle approved record deletions |

### Screens

Long pages are split into pages or tabs, so nobody scrolls through a whole form. Sign out stays in view in the side menu.

1. Login and password setup: sign-in, invite and password reset
2. My tasks: notifications at the top, then what each person must do next and when it's due; a lead sees one KPI task for each team member, each with its own due date
3. My team: a lead's team members, each with their stage
4. Evaluation form: one page for each part, with standards and KPIs shown three at a time; a collapsible "Rating levels" card explains the scale, and hovering over a rating shows its description and behavioural example
5. Comparison: both reviews side by side after the reveal, in tabs for standards, overall review, development and KPIs
6. Final ratings: three columns for each item (employee, lead, final), in tabs for competencies, objectives (KPIs), and overall and summary
7. Evaluation history: a KPI progress chart and a link for each year; each year opens on its own page
8. HR dashboard: a count for each stage that filters the table below when clicked, search by employee name, and 10 rows to a page
9. HR admin: cycles, the template, exceptions, correction notes, imports and the audit log
10. Administrator: HR role management and approved deletions

## Scope

The first version covers one annual review per cycle for one company of 50 employees.

### In scope

- Login with company email and a self-chosen password, set through an HR invite
- A new evaluation form designed for this process
- KPI setup, blind submission, reveal, HR-scheduled meeting, final ratings, HR confirmation and permanent lock
- An overall score calculated from the final ratings
- KPIs set in one cycle and marked Met, Partially met or Not done in the next
- Client PDF upload per employee and cycle
- Past evaluations as one web page per year, with a KPI progress chart
- HR dashboard, reminders, and cycle and template management
- Employee details and reporting lines read from the HRIS API, using a mock API first

### Out of scope

- Single sign-on
- Writing data back to the HRIS, or links to other HR systems
- Year-round KPI tracking
- Rating calibration
- A dispute or appeal route for final ratings
- Client logins
- Automatically reading scores from client PDFs
- Mobile apps

## Assumptions

These are the decisions the build follows, with no further HR approval needed. The last column shows the rework if one changes later.

| Topic | Default being built | Rework if changed later |
| --- | --- | --- |
| Submission order | The employee submits the self review first; the lead's review opens after that | Medium: changes visibility rules, stages and tests |
| HR during the blind stage | Sees status only | Low: widen one permission |
| Rating scale | Five proficiency levels, Basic to Expert, with a behavioural example for each level of each standard | Low: edit the template |
| Reviews per cycle | One annual review | High: changes cycles, history and the year links |
| Form template | One fixed template per cycle, for all roles | High if each role needs its own form |
| When KPIs are set | The lead sets each person's KPIs for the coming year before that person's review opens; they are reviewed in the next cycle | Medium: add a goal-setting stage earlier in the year |
| KPI progress | Marked Met, Partially met or Not done; only the final ratings give a KPI a 1 to 5 rating | Low: edit the template |
| Client PDFs before the reveal | Only HR and the current lead can open them, so the lead isn't blind to client feedback | Low: limit reading to HR |
| Employee reads client PDFs | Yes, from the reveal onward | Low: remove one permission |
| No agreement reached | Not handled: final ratings are agreed in the meeting, and the employee has no confirm or return step | Medium: add a return or dispute step |
| Who confirms | HR confirms after discussing with management; HR can add a note but can't change or return the ratings | Medium: add a return step for HR |
| Overall rating | Calculated: 50% average competency rating plus 50% average objective (KPI) rating | Low: change the weights |
| Pilot timing | Runs in the next scheduled review, while other teams keep their current process | High if no review falls within the pilot period |

Final ratings don't change after locking, even if they affect pay or promotion, and this system has no separate appeal route.

## Glossary

| Term | Meaning |
| --- | --- |
| Cycle | One review period that HR opens, such as the 2027 annual review |
| Form | One employee's evaluation record in a cycle |
| Side | The employee's or the lead's own answers on a form |
| Reveal | The moment both sides become visible, when the lead submits after the employee |
| Current team | A lead's direct reports, plus any form assigned through X5 |
| Included employee | A person HR selected for a cycle |
| Final states | Review done and Excluded |
| Review done | The last stage: HR has confirmed and the record is locked |
| Competencies | The performance standards, plus the management skills for people who manage others |
| Objectives | The KPIs set in the last cycle and reviewed in this one |
| KPI status | Met, Partially met or Not done |
| Overall score | The calculated result of a review, out of 5 |
| Stakeholders | HR, the project sponsor and the named gate approvers |
| HRIS | The company's HR information system, which holds employee details |
| Sync | The system reading employee details from the HRIS API |

## Workflow

Three gates define the product: the review form opens only after the lead publishes KPIs, the reveal happens only after both submit, and the record locks only after HR confirms.

```mermaid
flowchart TD
  HR["HR opens the cycle<br/>One form per employee"]
  KPI["KPI setup<br/>Lead sets KPIs for the coming year"]
  subgraph Blind["Blind stage: each person sees only their own answers; HR sees status"]
    EMP["Employee self review<br/>Save drafts, then submit"]
    LEAD["Lead manager review<br/>Opens after the employee submits"]
  end
  REV["Revealed<br/>Both reviews visible; HR is notified"]
  MEET["HR sets the meeting<br/>Employee and lead are notified"]
  FIN["Final ratings<br/>Lead enters them during the meeting"]
  CONF["HR confirmation<br/>HR and management discuss and confirm"]
  DONE["Review done<br/>Locked, permanent history"]
  HR --> KPI --> EMP --> LEAD --> REV --> MEET --> FIN --> CONF --> DONE
```

The employee submits first. The lead's review opens after that, and the lead sees the employee's answers only after submitting their own.

### Stages

Each form is always in exactly one stage, and any move not listed here is refused.

| Stage | Who acts | What moves it on | Blocked until then |
| --- | --- | --- | --- |
| KPI setup | Lead | Lead publishes 3 to 6 KPIs for the coming year | Employee can't open the review form |
| Blind | Employee, then lead | Employee submits the self review, then the lead submits the manager review | Neither side can read the other |
| Revealed | HR | HR sets the meeting date, time and place | The Final ratings page isn't available |
| Final ratings | Lead | Lead submits the agreed final ratings when the meeting is over | Employee and HR can't see the final column |
| HR confirmation | HR | HR confirms the review after discussing it with management | The record isn't locked |
| Review done | Nobody | End state | All changes except correction notes and approved deletions |
| Excluded | Nobody | HR re-includes the employee while the cycle is open | All evaluation actions |

Final ratings can't be changed once the lead submits them. In HR confirmation, HR can add a note but can't edit them.

### Stage moves

| From | To | Who | Condition |
| --- | --- | --- | --- |
| KPI setup | Blind | Lead | 3 to 6 KPIs published, each with a name and target |
| Blind | Revealed | System | The lead submits after the employee |
| Revealed | Final ratings | HR | Meeting date, time and place set |
| Final ratings | HR confirmation | Lead | Every final field complete |
| HR confirmation | Review done | HR | HR confirms |
| Any stage before Review done | Excluded | HR | HR records a reason (X3) |
| Excluded | The stage it left | HR | HR re-includes the employee while the cycle is open (X3) |

A form excluded before the reveal never reveals anything: submitted answers stay hidden from everyone and get no history page. A form excluded after the reveal keeps its page, labeled "Excluded".

Final states are Review done and Excluded. HR gets an alert when a lead misses a KPI, submission or final-ratings deadline, and again 5 working days later.

## Permissions

The server enforces these rules on every request; hiding something in the screen doesn't count as enforcement.

| Action | Employee | Lead | HR |
| --- | --- | --- | --- |
| Set and publish KPIs for the coming year | No | Current team | No |
| Open the review form before KPIs are published | No | Current team, to set KPIs | No |
| Fill in the evaluation before submitting | Own | Current team, after the employee submits | No |
| Read the other side's answers before the reveal | No | No | No |
| See a form's status at any stage | Own | Current team | All |
| Read answers from the reveal onward | Own | Current team | All |
| Set the meeting date, time and place | No | No | Yes |
| Open the Final ratings page once the meeting is set | Own | Current team | All |
| Enter final ratings | No | Current team | No |
| See the final column before the lead submits it | No | Current team | No |
| See final ratings and the overall score after the lead submits | Own | Current team | All |
| Confirm the review and add an HR note | No | No | Yes |
| Upload a client PDF | No | Current team | All |
| Read a client PDF from the reveal onward | Own | Current team | All |
| Read past evaluations and KPI progress | Own | Current team | All |
| Manage cycles and templates, and send invites | No | No | Yes |
| Change a submitted final rating or a locked record | No | No | No |
| Edit HRIS employee details or reporting lines | No | No | No |
| Read a client PDF before the reveal | No | Current team | All |
| Use HR controls X1 to X5 and X7, approve lead changes, add correction notes, run a sync, enter imported evaluations | No | No | Yes |

HR staff can't use HR rights on their own record. Another HR person acts on their record; if there is none, the administrator can use HR controls on that one form, without reading answers before the reveal. A lead who is replaced loses access to that employee's records. The administrator manages the HR role and approved deletions, and has no other access to evaluations. "Current team" means direct reports, plus any form assigned through X5.

## Exceptions

These rules cover the cases most likely to stall a cycle.

| Case | Proposed behavior |
| --- | --- |
| One person misses the deadline | Answers stay hidden, reminders continue, and HR can extend the date, or exclude the form if it will never be completed |
| Someone submits by mistake | HR can reopen it before the reveal only, and the audit log records it |
| Lead changes mid-cycle | After HR approves the HRIS change, access moves to the new lead; a submitted evaluation stays, and an unsubmitted draft doesn't pass to the new lead |
| Employee disagrees with the final ratings | Not handled in this version: the ratings are agreed in the meeting and the employee has no return step; see Open questions |
| Lead misses the KPI due date | The employee's form stays closed, the task shows as overdue for the lead, and HR is alerted |
| Mistake found after locking | Ratings stay unchanged, and HR adds a dated correction note beside them |
| New joiner or long leave | Joining after the KPI due date or long leave means exclusion by default, with a recorded reason; otherwise HR adds them through X4 |
| Employee leaves mid-cycle | The HRIS sync stops their login, and HR excludes the form with a recorded reason |
| Lead leaves or goes on long leave with no replacement | HR names a temporary lead for that form |

## Evaluation form

This is a new form designed around KPI setup, the blind rule and final agreement. Employee and lead answer the same questions separately, so the two reviews compare line by line after the reveal.

### Form structure

| Part | What it asks | Employee | Lead | Required |
| --- | --- | --- | --- | --- |
| 1. Details | Name, employee ID, job title, department, lead and review period, filled in from the HRIS | Reads | Reads | Automatic |
| 2. Performance standards | Nine standards, each with a rating and an example | Fills in own view | Fills in own view | Rating always; example for a rating of 1, 2 or 5 |
| 3. Management skills | Four more standards, shown only when the employee manages others | Fills in own view | Fills in own view | As part 2 |
| 4. Overall review | Three questions on performance, development objectives and values, each with a rating and a comment | Fills in own view | Fills in own view | Yes |
| 5. Development | 1 to 3 objectives for next year, each with an action and any support needed | Proposes | Proposes separately | Objective and action |
| 6. KPIs from the last cycle | For each KPI: a status and a description of the result | Fills in own view | Fills in own view | Yes, when KPIs were set last cycle |
| 7. KPIs for the coming year | 3 to 6 KPIs, each with a name and target | Reads once published | Sets and publishes in KPI setup | Yes |
| 8. Client evaluations | Attached client PDFs | Reads after the reveal | Reads | No |
| 9. Final ratings | For each standard and KPI: the employee's answer, the lead's answer, a final rating and a comment, plus a final status for each KPI; agreed development objectives; a summary; the overall score | Reads after the meeting | Fills in during the meeting | Yes |

The nine performance standards are job knowledge, quality of work, communication, problem solving, service orientation, teamwork, initiative and reliability, attendance and punctuality, and commitment to company goals. The four management skills are strategic thinking, fiscal responsibility, individual leadership, and planning and organising.

Neither the employee nor the lead rates a KPI from 1 to 5 in their own review; they mark its status only. A KPI gets a 1 to 5 rating once, in the final ratings.

Where both sides gave the same rating or status, the final column starts with that value, and the lead can change it during the meeting.

### Rating levels

Every 1 to 5 rating uses one scale of proficiency levels. Level 3 is the expected level for the role.

| Rating | Level | Meaning |
| --- | --- | --- |
| 1 | Basic | Needs guidance |
| 2 | Developing | Applies the skill with some support |
| 3 | Proficient | Applies it independently; the expected level for the role |
| 4 | Advanced | Applies it in complex situations, and helps others |
| 5 | Expert | Sets the standard, and shapes how the organisation does it |

Each standard has a behavioural example for each level, 65 in all, so people rate against described behaviour instead of a bare number. The form shows the level descriptions once, in a "Rating levels" card above the progress strip, and shows a level's description and example when someone hovers over or tabs to that rating.

A rating of 1, 2 or 5 needs a written example or comment, including in the final ratings.

### KPI status

| Status | Meaning |
| --- | --- |
| Met | The target was reached |
| Partially met | Some of the target was reached |
| Not done | The target wasn't reached |

KPIs set in one cycle are marked in the next. KPIs set in the 2026 review are marked in the 2027 review. A person's first review has no earlier KPIs to mark.

### Overall score

The system calculates the overall score from the final ratings; nobody selects it.

```text
Overall score = (competency weight × average competency final rating)
              + (objective weight × average objective final rating)
```

- Competencies are the performance standards, plus the management skills where they apply. Their weight is 50%.
- Objectives are the KPIs from the last cycle. Their weight is 50%.
- Example: competencies average 3.3 and objectives average 3.5, so the score is (50% × 3.3) + (50% × 3.5) = 3.4 out of 5.
- The score is rounded to one decimal place and shown with the name of the nearest level and the working.
- With no KPIs from the last cycle, competencies carry 100%.
- The score appears only when every final rating is filled in, and is stored with the record when the lead submits.

## Functional requirements

Each requirement below is written so it can be tested directly.

### Accounts and login

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| A1 | Accounts and reporting lines come from the HRIS sync | Nobody can sign up without an HRIS record and an HR invite; each employee has one current lead, and the most senior person is excluded from cycles in the MVP; naming an evaluator for them is a pilot option |
| A2 | Users set their own password through an invite | The invite link works once and expires after 7 days; only approved company email domains are accepted |
| A3 | Users sign in with company email and password, and can reset the password | A wrong password never reveals whether an email exists; 5 failed attempts block login for 15 minutes |
| A4 | One person can be both employee and lead | The person sees their own form and their team's forms, each with the correct rights |
| A5 | HR sends and resends invites, and sees each person's invite status | Status shows not sent, sent, accepted or expired; an expired invite can be resent |

### Cycles and templates

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| C1 | HR creates a cycle with a name, year, template and deadlines for KPIs, submission, final ratings and HR confirmation | HR selects which employees a cycle includes, and opening it creates one form for each, using the latest valid HRIS data |
| C2 | HR can edit labels, rating meanings and competencies by creating a new template version | Changing a template never alters a form already created from an earlier version |
| C3 | Each form keeps its template version | A past evaluation displays exactly the questions and scale it was completed on |
| C4 | Leads publish 3 to 6 KPIs for the coming year for each team member, by that member's KPI due date | My tasks shows one KPI task for each team member with its due date; each KPI needs a name and a target; the employee can't open the review form until the KPIs are published, and is notified when they are; HR is alerted if the due date passes |
| C5 | Published KPIs can't be changed by the lead | The lead sees a warning before publishing; a later change needs HR, a log entry and a notice to both people |
| C6 | KPIs set in one cycle are reviewed in the next | The next cycle's form lists them for both sides to mark Met, Partially met or Not done, with a result |

### Blind evaluation

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| E1 | The employee fills in the self review, then the lead fills in the manager review, and drafts save automatically | A draft survives logout and is visible only to its author; a warning appears before the session times out, and typed work isn't lost |
| E2 | Submitting requires all mandatory fields | Missing fields are named with a link to the page that holds each one, and nothing is submitted until they're complete |
| E3 | The system asks for confirmation before submitting, warns the lead that submitting reveals both reviews, and makes a submitted side read-only | Edit requests are refused, except after an HR reopen before the reveal |
| E4 | Neither side can read the other before the reveal | Screens, API responses, exports, notifications and logs contain none of the other side's answers, including through a direct API call |
| E5 | The form reveals automatically when the lead submits after the employee | Employee, lead and HR are notified; the two reviews display side by side; HR gets a task to set up the meeting |
| E6 | During Blind, each person sees only whether the other side has submitted | The screen shows submitted or not submitted, with no answers, ratings, draft progress or time; the lead's review stays closed until the employee submits |
| E7 | Each part of the form is its own page | Standards and KPIs show three at a time with Back and Next; a missing field opens the page that holds it |
| E8 | The form explains the rating levels | A collapsible "Rating levels" card sits above the progress strip; hovering over or tabbing to a rating shows its description and behavioural example |

### Client evaluations

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| P1 | HR or the lead uploads client PDFs to an employee's form | Only PDF files up to 20 MB are accepted, and from the pilot onward they must pass a malware scan; client name and evaluation date are recorded |
| P2 | One form can hold more than one client PDF | Each PDF is listed separately and opens in the browser |
| P3 | Client PDFs follow the blind rule | Before the reveal, only HR and the current lead can list or open a PDF, including its name and count; a PDF added after the reveal is visible at once to the employee, lead and HR |
| P4 | Client PDFs lock with the form | No upload, replacement or removal is possible after locking; for a PDF arriving later, HR can record its receipt in a correction note |
| P5 | PDF links are protected | PDFs open only through a route that checks the signed-in user on every request, so a copied address fails for anyone without access |
| P6 | HR or the lead can remove a wrong PDF before locking | Before the reveal, HR or the lead can remove it; after the reveal, only HR can; removal needs a reason, appears in the audit log and hides the file instead of deleting it; a PDF covering several employees must be split before upload |

### Meeting, final ratings and lock

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| F1 | HR sets the meeting once both reviews are submitted | HR enters a date, time and place; the employee and lead are notified; the form moves to Final ratings and the Final ratings page opens for the employee, lead and HR |
| F2 | The lead enters the agreed final ratings during the meeting | For each standard: a final rating and a comment, beside the employee's and the lead's ratings; for each KPI: a final status, a final rating and a comment; plus agreed development objectives and a summary; every required field must be filled in before submitting |
| F3 | Final ratings stay hidden until the lead submits them | Before then, the employee and HR see the employee and lead columns, with the final column marked "Shown after the meeting"; after the lead submits, all three see every final rating, comment and the overall score |
| F4 | HR confirmation locks the review permanently | Final ratings can't change once the lead submits them; HR can add an optional note from the management discussion, then confirms; the form moves to Review done and the employee and lead are notified; every write to a locked form fails for every role, including HR and direct API calls, except correction notes and approved deletions |
| F5 | HR can add a dated correction note after locking | The note appears beside the record, the original ratings remain unchanged, and the employee and lead are notified |
| F6 | The system calculates the overall score | It follows the formula in Overall score, shows the working, appears only when every final rating is filled in, and is stored when the lead submits |
| F7 | The final column starts with agreed values | Where the employee and lead gave the same rating or status, that value is filled in for the lead to keep or change |

### History and KPIs

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| H1 | Every evaluation at Review done becomes a read-only web page | The page shows both reviews, final ratings, the overall score and its working, KPIs reviewed and KPIs set, meeting details, any HR note and client PDFs, in tabs |
| H2 | The history page lists a link for each year | Each year opens on its own page, with that year's KPIs, ratings and comments; years are never shown all on one page |
| H3 | The history page shows a KPI progress chart | One row for each year KPIs were set and one block for each KPI, showing Met, Partially met, Not done or not yet reviewed; each status has a text label as well as a colour; values match the locked form exactly |
| H4 | The history page shows overall scores across years | Years appear in order; individual KPIs aren't compared because they can change yearly; imported ratings keep their original scale label and aren't converted |
| H5 | Past-year access follows the permissions table | The employee, their current lead and HR can read the history; a direct link fails for anyone else |
| H6 | HR enters evaluations from before the system through a simple import form | Required fields are year, employee, lead and overall rating; other fields stay blank if missing; HR previews each page before saving; pages are labeled "Imported", keep their original rating scale and lock on saving; a past lead is stored as a name, without access; only HR can create them |
| H7 | HR can export one employee's full record | The export covers evaluations, KPIs, final ratings, notes and PDFs; an employee can ask HR for a copy or a correction note, and the audit log records it |

### HR tracking

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| R1 | The dashboard shows each employee's stage across the cycle | A count for each stage sits above the table, and clicking a count filters the table to that stage; HR can also search by employee name and filter by team, stage and overdue; the table shows 10 rows to a page; HR sees no answers before the reveal |
| R2 | The system sends notifications | Each one in the Notifications table goes to the people listed, at the time listed, by email and in the Notifications list on My tasks, with an unread count in the menu; none contains answers or ratings |
| R3 | HR can export completion status | The export contains status only, not evaluation answers |

### HR controls

These actions handle exceptions, and each one needs a reason and appears in the audit log.

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| X1 | HR can extend a form's deadline | The new date shows on the form and resets its reminders |
| X2 | HR can reopen a submitted side before the reveal | Only that side's author can edit it, and both people are notified without seeing any answers |
| X3 | HR can exclude an employee from a cycle | The form moves to Excluded, no longer counts as overdue, and reveals nothing new; HR can re-include the employee while the cycle is open |
| X4 | HR can add an employee to an open cycle | A new joiner gets a form using the cycle's template |
| X5 | HR can reassign a form when no lead is available | HR names a temporary lead, who gets access to that form only; the absent lead loses access to it, a sync doesn't undo the assignment, and the temporary lead starts blank unless the lead side was already submitted |
| X6 | Removed: there is no dispute stage in this version | Not applicable |
| X7 | HR can suspend a person's access immediately | Login stops and open sessions end at once, without waiting for a sync; HR can restore access, and a sync never reactivates a suspended person |
| X8 | HR can read the audit log | Only HR and the administrator can read it, and nobody can edit it |
| X9 | The system writes an audit event for every action in the audit log requirement | Each listed action, including refused access attempts, creates one event that can't be edited |
| X10 | Removed: there is no dispute stage in this version | Not applicable |

### Notifications

Emails carry only the event, the form's employee name where needed and a link that requires login.

| Event | Who is notified | When |
| --- | --- | --- |
| Invite or password reset | That person | On request |
| Email address changed | Old and new addresses | After the sync |
| Cycle opened | Included employees and their leads | When HR opens it |
| KPIs published, so the review form is open | Employee | At once |
| Deadline approaching | People who haven't acted | 7 and 2 days before |
| Deadline passed | People who haven't acted, and HR for lead steps | Every 3 working days |
| Side reopened | Both people | At once |
| Employee submits the self review | Lead | At once |
| Reveal, when both reviews are in | Employee, lead and HR | At once |
| Meeting set, with date, time and place | Employee and lead | At once |
| Final ratings submitted by the lead | Employee and HR | At once |
| Review confirmed by HR | Employee and lead | At once |
| A lead change to approve | HR | At once |
| Form assigned to a temporary lead | Employee, temporary lead and absent lead | At once |
| Client PDF added after the reveal | Employee and lead | At once |
| Correction note added | Employee and lead | At once |
| Bounced email, or a failed or paused sync | HR | At once |

A bounced email is shown on the HR dashboard, and the system retries it once.

## HRIS integration

The HRIS is the source of truth for employee details and reporting lines, and this system only reads from it. The demo and early build use a mock API with 50 made-up employees, and real HRIS integration comes later, as separate work before the pilot.

Passwords stay in this system because the HRIS doesn't handle login here. Evaluations, ratings and KPIs are never sent to the HRIS.

### Employee data

The API returns only the fields below; salary, bank and other unneeded personal data must not be requested. The real HRIS may use different names, so the fields are a proposal until its API is confirmed.

| Field | Used for | Required |
| --- | --- | --- |
| Employee ID | Matching a person across syncs and years | Yes |
| Full name | Forms, dashboard and history | Yes |
| Company email | Invite and login | Yes |
| Lead's employee ID | Reporting line and form access | Yes, except the most senior person |
| Employment status | Active and on-leave people keep login; a terminated person loses it | Yes |
| Job title | Form and history display | Yes |
| Department or team | HR dashboard filter | Yes |
| Start date | Identifying new joiners | Yes |
| Last updated time | Fetching only changed records | If the HRIS supports it |

### Sync requirements

| ID | Requirement | Acceptance criteria |
| --- | --- | --- |
| S1 | The system syncs employee data every day, and HR can start a sync manually | Each run records its start time, result and number of records changed |
| S2 | The system matches people by employee ID | A changed name or email updates the existing person, their history stays attached, and an email change notifies both addresses |
| S3 | New employees are added automatically | HR sees them on the dashboard and sends the invite; no account becomes active without one |
| S4 | Employees marked inactive lose access | Login stops after the sync, and their evaluation records remain; a rehired person with the same employee ID keeps their history and needs a new invite |
| S5 | A lead change updates access | The old lead keeps access until HR approves or rejects the change; approval moves access, rejection means HR corrects the HRIS, and deadlines continue unless HR extends them |
| S6 | Locked records keep their original details | A later sync never changes the name, job title or lead shown on a locked evaluation |
| S7 | Invalid records don't apply | A record with a missing required field, duplicate email or unknown lead keeps its last valid version and appears on an HR error list, but inactive status always applies |
| S8 | A failed sync does no harm | The system keeps its last valid data and shows HR the failure; login and open evaluations continue to work |
| S9 | HRIS fields are read-only here | Screens and APIs refuse any edit; corrections are made in the HRIS and arrive with the next sync |
| S10 | The design makes the mock API easy to replace | HRIS connection code stays apart from workflow code; the effort to switch is confirmed once the real API is reviewed |
| S11 | Unusual results raise an alert | After the first load, a missing person, an empty response or changes to more than 20% of records pause the sync; HR approves or discards the pending changes |
| S12 | HR reviews reporting lines before opening a cycle | A cycle can't open until HR confirms the list |

The HRIS doesn't decide who has the HR role in this system. An administrator assigns it here, while lead access comes from the reporting lines.

### Mock API

The mock provides a repeatable way to test every sync outcome without access to real employee data. Its endpoints are `GET /employees`, with an optional changed-since filter, and `GET /employees/{id}`, both requiring an API key.

| Mock scenario | Expected result |
| --- | --- |
| Normal list of 50 employees | All 50 are created with correct reporting lines |
| New joiner | Person is added and shown to HR for an invite |
| Employee leaves | Login stops, and records remain |
| Lead changes mid-cycle | Access moves, and the open form is flagged |
| Name or email changes | Existing person is updated, and locked records don't change |
| Missing field, duplicate email or unknown lead | Record isn't applied and appears on the error list |
| API down, timeout or wrong API key | Sync fails safely, and HR sees the failure |
| Person missing, or an empty response | Sync pauses, and HR is alerted |

## Quality requirements

Evaluations are sensitive personal data, so security and privacy rules apply from the demo onward. The numeric targets are proposals suited to 50 users. Access control, encryption, passwords, the audit log and demo data apply from the demo, except two-step verification; the rest apply before the pilot.

| Area | Requirement |
| --- | --- |
| Access control | The database and server enforce the permissions table, so the screens aren't the only barrier |
| Passwords | At least 12 characters, stored only as secure hashes, and unreadable by anyone including HR; sessions end after 30 idle minutes; HR and administrator logins use two-step verification from the pilot onward |
| Encryption | HTTPS for all traffic; data and PDFs encrypted when stored |
| Audit log | Records reveals, HR control actions, lead-change approvals, KPI publishing and changes, meeting setup, PDF uploads and removals, final ratings, HR confirmations, correction notes, template and HR role changes, deletions, HR reads of evaluations and refused access attempts; nobody can edit it |
| Privacy | Before real data is used, hosting location and retention periods are checked against Sri Lanka's Personal Data Protection Act; vendor contracts, a breach response plan and an employee privacy notice are in place |
| Backups | Daily backups with a tested restore before the pilot; at most 24 hours of data lost and service restored within 1 working day; after any restore, HR checks forms against confirmation notices, and confirms again any review whose confirmation was lost |
| Performance | Pages load within 2 seconds with 50 active users on a 20 Mbps connection; the system is available 99% of the time during review periods |
| Browsers | Current Chrome, Edge, Safari and Firefox; pages usable on a phone browser |
| Accessibility | Forms and history pages work by keyboard and meet WCAG 2.1 AA |
| Demo data | Made-up people and comments only; no real evaluations are used; demo data stays in a separate environment and never moves to the pilot |
| HRIS connection | Read-only access; the API key stays on the server; only the listed fields are requested and stored |
| Production access | Database admin tools can bypass the app's rules, so at most two named people hold that access; use is logged and reviewed at each gate |
| Record deletion | Locked records are deleted only when a retention rule or law requires it, by the administrator, with HR approval and a log entry |
| Time and language | Sri Lanka time (UTC+5:30), with deadlines ending at 23:59; working days are Monday to Friday, excluding company holidays, which are seeded for the MVP and entered by HR from the pilot; the app is in English only |
| Errors and monitoring | Users see a plain error message and keep their typed work; the support owner is alerted to app errors and failed syncs within 15 minutes |

The system will be used in Sri Lanka. Sri Lanka's Personal Data Protection Act No. 9 of 2022, as [amended by Act No. 22 of 2025](https://dpa.gov.lk/acts/22-2025_E_251104_201549.pdf), has [key provisions taking effect on 1 January 2027](https://www.ft.lk/front-page/Data-protection-compliance-regime-takes-effect-on-1-Jan-2027/44-795776). They cover lawful processing, Data Protection Officers, breach notices, impact assessments, processor contracts and cross-border transfers.

The legal review should confirm how the Act applies to employee evaluations, and whether hosting outside Sri Lanka needs extra safeguards.

## Data model

Each evaluation keeps a fixed copy of its template, so changes to a template can't rewrite history.

| Record | Holds | Key rule |
| --- | --- | --- |
| User | HRIS employee ID, name, company email, job title, department, roles, active status | One person can be both employee and lead |
| Reporting line | Employee, lead, start and end dates | Comes from the HRIS; past leads stay on record without keeping access |
| Template version | Sections, questions and rating lists | Can't be edited once used by a form |
| Cycle | Name, year, deadlines, template version | One annual review in the first version |
| Evaluation form | Employee, lead, temporary lead, cycle, current stage, deadlines, KPI due date, meeting date, time and place, imported label | One per employee, per cycle; keeps a copy of employee and lead details once locked |
| Evaluation side | Author type and ID, answers, KPI statuses and results, submitted time | One employee side and one lead side per form, in separate records so the database can hide each one |
| KPI | KPI name and target, set by the lead | Set on one cycle's form and reviewed on the next cycle's form |
| Client evaluation | Client name, date, stored PDF, uploader | Follows the form's reveal and lock rules |
| Final result | Final rating and comment for each standard; final status, rating and comment for each KPI; overall score with its averages and weights; development objectives; summary; HR note; submitted and confirmed times | Hidden from the employee and HR until the lead submits; can't change after that |
| Notification | Recipient, date, text, linked form and page, read flag | Never holds answers or ratings |
| Correction note | Text, author, date | Added beside a locked result, never replacing it |
| Audit event | Who, what, which record, why, when | Add-only |
| HRIS sync run | Start time, result, records changed, rejected records | A failed run leaves existing data unchanged |
| Invite | Person, status, sent and expiry times | A resend replaces the earlier link |
| Pending lead change | Employee, old lead, new lead, HR decision, date | Access moves only after approval |

## Technology

The recommendation is one web application and one managed database, which is the simplest setup that meets these rules for 50 users.

| Part | Choice | Why |
| --- | --- | --- |
| Web app | Next.js with TypeScript | One codebase for screens and server logic |
| Database, login and PDF storage | Supabase (PostgreSQL) | Covers all three, and row-level security can enforce the blind rule in the database |
| Hosting | Vercel | Simple deployment and an easy demo link |
| Email | Transactional email service, to be chosen | Sends invites, resets and reminders |
| Testing | Playwright | Covers UI and API tests in one tool |
| Employee data | Mock API first, then the real HRIS API | The build can start without HRIS access, and sync failures can be tested on demand |

## Test strategy

The highest-priority tests prove that the blind rule and the lock can't be bypassed, because a single failure would damage trust in every evaluation.

| Level | Focus | Example |
| --- | --- | --- |
| API permissions | Every role against every stage and record | An employee requests the lead's answers before the reveal, including through Supabase's automatic API, and is refused |
| Workflow | Every allowed and blocked stage change | The employee's form is refused before KPIs are published, and final ratings are refused before HR sets the meeting |
| Lock | Every write path on a locked form | HR's request to change a final rating is refused |
| Data leak | Other routes to hidden answers | Emails, exports, logs and PDF links reveal nothing early, and the employee and HR can't read final ratings before the lead submits them |
| Scoring | The overall score | Final ratings averaging 3.3 for competencies and 3.5 for objectives give 3.4; a form with no earlier KPIs uses competencies only |
| End to end | The full cycle across three roles | Publish KPIs, submit both reviews, reveal, set the meeting, enter and submit final ratings, confirm as HR, then read the history page |
| Template history | Old forms after template changes | A past evaluation still displays its original questions |
| Accessibility and browsers | Main screens | Keyboard-only completion of the form passes, and an accessibility audit runs before the pilot |
| HRIS sync | Every mock scenario, plus contract checks against the real API before the pilot | A lead change moves access, and locked records stay unchanged |
| Simultaneous actions | Actions that collide in time | Both sides submit together, or HR reopens during a submit, and the form ends in one valid stage |
| Operations | Restore, password reset, email, upload limits, performance and import | A restored backup keeps locked records locked |

The API permission, lock and data leak tests run on every code change, and any failure blocks the release. Every requirement ID and quality requirement links to at least one test.

## Delivery

Three phases move from made-up data to all 50 employees, and each needs a gate approval. All current work uses the mock API, and real HRIS integration comes later, before the pilot starts. Dates are still to be set, after the open questions below are answered.

```mermaid
flowchart LR
  D["1. Demo<br/>Full workflow on the new form<br/>Made-up data, mock HRIS API"]
  G1{"Gate 1<br/>HR runs a full cycle unaided<br/>Stakeholders approve<br/>Security and backups pass"}
  P["2. Pilot<br/>One team, one real cycle<br/>Real HRIS API and team data"]
  G2{"Gate 2<br/>Pilot team completes a cycle<br/>HR approves the rollout"}
  R["3. Rollout<br/>All 50 employees<br/>Past evaluations imported"]
  D --> G1 --> P --> G2 --> R
```

### Requirements by phase

The demo proves the main path with one fixed template, and every demo requirement must pass for Gate 1. Later phases add exception handling and what real data needs. Requirements carry forward once delivered.

| Phase | Requirements | Also needed |
| --- | --- | --- |
| Demo | A1 to A5, C1, C3 to C6, E1 to E8, P1 to P4, F1 to F7, H1 to H3, H5, R1, R2, X2, X8, X9, S1 to S3, S9, S10 | Mock API with 50 made-up employees, test logins and inboxes for each role, and a way to trigger each mock scenario; security, backup and accessibility checks before Gate 1 |
| Pilot | C2, H4, H7, R3, P5, P6, X1, X3 to X5, X7, S4 to S8, S11, S12 | Real HRIS API, malware scanning, two-step login, short user guide and training |
| Rollout | H6 | Support owner in place, past evaluations imported |

With one annual review, pilot and rollout could be up to a year apart unless HR runs an off-cycle pilot.

If the system fails during the pilot, the pilot team finishes that cycle in its current process. Pilot records already locked stay locked, and any errors get correction notes.

## Risks

The largest risk is hidden answers leaking before the reveal, because one leak would damage trust in every evaluation.

| Risk | Mitigation |
| --- | --- |
| Answers leak before the reveal | Enforce access in the database and block releases on API tests |
| A demo reaches real use without being hardened | Build with real access rules from the start, and require Gate 1's security and backup checks |
| Real evaluations end up in the demo | Use only made-up data, and keep demo and live environments separate |
| The system has no long-term owner | Name who supports and maintains it before Gate 1 |
| Leads or employees don't adopt it | Pilot with one team first, and test the new form with a few leads and employees |
| The real HRIS API differs from the mock | Get its documentation early, keep field mapping apart from workflow code, and run contract checks before the pilot |
| HRIS reporting lines are wrong or out of date | Have HR review reporting lines before opening a cycle, because a wrong lead could see someone's evaluation |

## Dependencies

Dates remain to be set, and the owners below are proposed roles until people are named.

| Dependency | Owner | Needed by |
| --- | --- | --- |
| HRIS API documentation, test access and field approval | HR and IT | Before HRIS integration |
| Named gate approvers and administrator | HR | Gate 1 |
| Named builder, needed now for the demo | Project sponsor | Gate 1 |
| Hosting, email and malware-scanning services | Project owner | Demo for hosting and email; pilot for scanning |
| Legal and privacy review under Sri Lanka's Personal Data Protection Act | HR and legal adviser | Gate 1 |
| Review calendar for the pilot | HR | Gate 1 |
| Support and maintenance owner | Project sponsor | Gate 1 |
| Baseline of HR hours from the last cycle | HR | Gate 1 |
| Technical check that the stack meets session ending, lockout, 20 MB uploads and PDF backups | Builder | Before the demo build |

## Open questions

The build continues on the defaults in Assumptions, and each question should be closed before the gate shown. Reviews per cycle, per-role forms and pilot timing should come first, because they carry the most rework.

- [ ] Decide whether the lead may see client PDFs before submitting; a lead who receives or uploads one already has (Gate 1)
- [ ] Confirm that employees may read client PDFs, and that clients have been told (Gate 1)
- [ ] Decide the number of reviews per cycle (Gate 1)
- [ ] Choose which approved email domains can sign in (Gate 1)
- [ ] Check hosting location and retention periods against Sri Lanka's Personal Data Protection Act (Gate 1)
- [ ] Share HR's review calendar and confirm the pilot's timing (Gate 1)
- [ ] Confirm who builds this (Gate 1)
- [ ] Confirm how many HR staff there are, and name the administrator and gate approvers (Gate 1)
- [ ] Decide whether ratings affect pay or promotion, and whether an appeal route is needed (Gate 1)
- [ ] Decide how an employee records disagreement with the final ratings, now that there is no confirm or return step (Gate 1)
- [ ] Decide whether HR can return final ratings to the lead instead of only confirming them (Gate 1)
- [ ] Decide whether the final column stays closed until the meeting day (Gate 1)
- [ ] Confirm the 50% and 50% weights, and whether HR can change them for each cycle (Gate 1)
- [ ] Decide whether a KPI's final 1 to 5 rating should use its own labels instead of Basic to Expert (Gate 1)
- [ ] Decide whether "Blind" and "Revealed" need plainer names on screen (Gate 1)
- [ ] Confirm whether each role needs its own form or competencies (Gate 1)
- [ ] Identify the HRIS product, and get its API documentation, test access and approval for the listed fields (before HRIS integration)
- [ ] Confirm that the HRIS holds a correct lead for all 50 employees (before HRIS integration)
- [ ] Confirm that a daily sync is often enough, and that the HRIS allows it (before HRIS integration)
- [ ] Name who supports and maintains the system (Gate 1)
- [ ] Decide how many past years to import (Gate 2)
