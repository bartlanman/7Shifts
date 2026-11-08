# 7shifts Knowledge Base & Operator Guide

Nov 8, 2026 · @bart

This is a working reference for running 7shifts at Basic City: where every setting lives, the order things must happen in, the gotchas that cost hours or money, and how to reach the data through the API. Built from the official [7shifts Knowledge Base](https://kb.7shifts.com/hc/en-us) and [developer docs](https://developers.7shifts.com/) on Nov 8, 2026; plan prices come from third-party listings because 7shifts' pricing page doesn't publish them statically.

## Quick reference

| Need | Where |
| --- | --- |
| Support chat | Help (bottom left, web) or More → 7helps (mobile); Mon–Fri 9 AM–7 PM ET, Sat–Sun 11 AM–7 PM ET |
| Support phone | 1-888-979-5877 |
| Help center | [kb.7shifts.com](https://kb.7shifts.com/hc/en-us) |
| API tokens and webhooks | Settings → Developer Tools ([direct link](https://app.7shifts.com/settings/developer_tools)) |
| API docs and status | [developers.7shifts.com](https://developers.7shifts.com/) · [status.7shifts.com](https://status.7shifts.com/) |
| Plans and billing | Settings → Billing / Plans (Admins only) |
| Compliance jurisdiction | Settings → Company Settings → Labor & Compliance |
| POS and payroll connections | Apps & Integrations (mapping under Mapping) |

**The five rules that prevent most problems**

1. Map every POS employee and job to a 7shifts user and role before turning on sync.
2. Saving a shift isn't publishing it — publish, and republish after edits.
3. Only approved punches in a closed timesheet reach reports, exports and payroll; Approve All only covers the page you're on.
4. Never delete roles or shifts to fix data — archive roles, extend location hours instead.
5. Check Time Clocking → Pay Periods → Warnings before every payroll run.

## Platform overview

7shifts is a restaurant-specific workforce platform: scheduling, time clocking, labor compliance, tip management, payroll, team communication and labor reporting in one system, with POS integrations feeding sales and punches in.

**How an account is shaped** (this hierarchy drives permissions, schedules and the API):

| Level | What it is | Notes |
| --- | --- | --- |
| Company | One customer's 7shifts account | Top-level scope; nearly every API call is scoped to its company ID |
| Location | One physical venue | Each location is billed and scheduled separately |
| Department | Optional grouping inside a location | e.g. FOH, BOH, Bar |
| Role | A job someone works | Belongs to a department or directly to a location |
| User / Employee | A person | Can hold several roles across several locations |

Source: [7shifts developer docs](https://developers.7shifts.com/).

**Plans (new-customer list prices, per location per month).** In July 2025 7shifts renamed its tiers from Comp / Entrée / The Works / Gourmet to the current four.

| Plan | Monthly list price | Positioning |
| --- | --- | --- |
| Comp | Free | Up to 15 employees, one location, basic scheduling, time-off and availability |
| Essentials | $44.99 | Core paid scheduling + time clock |
| Pro | $89.99 | Adds labor budgeting, forecasting, engagement, more automation (14-day free trial is on Pro) |
| Premium | $149.99 | Full suite, multi-location controls; tip management included |

Add-ons reported by third parties in 2026: Payroll around $39.99/location/month plus about $6 per employee paid; tip management around $49.99/location/month below Premium. Annual billing is discounted. Prices vary by source and existing accounts may be on legacy rates, so confirm in-app under **Settings → Billing / Plans**. Sources: [Connecteam review (June 2026)](https://connecteam.com/reviews/7shifts/), [CostBench](https://costbench.com/software/employee-scheduling/7shifts/), [HiveDesk review](https://www.hivedesk.com/7shifts-review).

## Getting started and admin setup

Build the structure first (locations → departments → roles), then connect the POS, then add people — all on the web, since mobile shows settings read-only. 7shifts' own 30-day rollout: days 1–4 structure, POS and team; days 5–7 roles, invites and first published schedule; days 8–30 payroll and time-clock checks, posting schedules in both 7shifts and the old channel for the first two weeks. [7shifts 101 for Admins](https://kb.7shifts.com/hc/en-us/articles/4417519871763)

### Who can do what

| User type | Can manage | Can't |
| --- | --- | --- |
| Account Owner | Everything; the only one who can promote, demote or edit Admins. One per account; transfer ownership before deactivating | — |
| Admin | Company Settings, Billing, Plans, Locations, Developer Tools, Time Clocking settings, Payroll, integrations; adds and sets permissions for everyone below | Edit other Admins |
| Manager | Assistant Managers and Employees in their locations/departments; can only grant permissions they hold | Settings, billing, payroll setup, developer tools; approve another Manager's time off |
| Assistant Manager | Employees only; same default access as a Manager (plan-dependent) | Grant permissions |
| Employee | Own schedule, hours, availability, time off, shift pool | Any management |

Set permissions: Team → employee → **Permissions** tab → user type → tick permissions → Save, then assign them to a department or they can't build its schedule. Watch-outs: "Can manage time punches" covers every user type; "Can run reports" can expose total wages even with "Can view wages" off; time-off requests with no approving manager go to Admins. [Manager permissions](https://kb.7shifts.com/hc/en-us/articles/4417519940499) · [Access levels](https://kb.7shifts.com/hc/en-us/articles/29872209637651)

### Locations, departments, roles

- **Location:** Settings → Locations, Departments & Roles → + Add → name, address, time zone. You can copy department and role names from another location (not assignments or settings). Each location is billed and must be on the same plan; save each settings tab (General, Scheduling Hours, Holiday Pay, Shift Feedback, Labor & Compliance, Onboarding) separately.
- **Department:** Departments tab → + Add. The star sets the default. Deleting is permanent and asks for a replacement. Give each scheduling manager their own department.
- **Role:** Roles tab → + Add (name, department, color). Match POS job names exactly if you sync labor. **Don't delete roles** — it strips them from shifts and removes linked punches; move them to an "Archived" department instead. Moving a role between departments needs Support.

### Adding staff

| Method | Where | Limits |
| --- | --- | --- |
| AI Employee Importer | Team → Bulk add/import (trial only) | Photo, PDF or spreadsheet up to 5 MB; names, emails, phones only; once per location |
| CSV template | Team → Bulk add/import | 250 per upload; includes department, role, wage; everyone imports as Employee; re-upload doesn't update |
| One at a time | Team or Schedule | **Change Pay** schedules future raises or back-dates up to 30 days |
| Join link | Schedule → Add Employees | 30 days, 200 uses; you approve each request |
| POS import | Integration mapping | Names and roles must match the POS exactly; with wage sync on, edit wages in the POS |

Everyone needs a unique name and valid email (plus mobile for texts and push). Profiles can't be deleted — deactivate, then Team → Inactive Employees → Reactivate. [Add your staff](https://kb.7shifts.com/hc/en-us/articles/31119735342227)

## Scheduling

Shifts are yellow drafts until published, and nothing an employee sees changes until you publish again — saving is not publishing. The weekly loop: fill from a template or copy last week, adjust against the labor budget and warnings, publish, then handle shift pool and time-off requests.

### Build

- **Add a shift (web):** Schedule → click an empty cell → employee, role, times, breaks, notes; optional repeat by weekday. Shift+drag copies (Option on Mac), plain drag moves, right-click for copy/paste/duplicate/publish. The full week grid is web-only. [Add shifts](https://kb.7shifts.com/hc/en-us/articles/4417514096915-Add-shifts-to-a-schedule)
- **End times:** **BD** = business decline (no set end; enter a hidden estimate for labor math); **CL** = close (set per location).
- **Templates:** Copy icon → Manage Template → + Add (one location and department; can hold labor % and projected sales). Apply with Copy → **Fill from Template** → Merge or replace. Scheduled breaks don't copy. [Templates](https://kb.7shifts.com/hc/en-us/articles/4417514429459-How-to-create-and-use-scheduling-templates)
- **Copy schedule:** Copy → Copy to, up to 3 weeks ahead in the same department; result is a draft. [Copying](https://kb.7shifts.com/hc/en-us/articles/4417513461267-Copying-a-Schedule)
- **Revert / Clear** (Tools icon): Revert restores the last published version; **Clear wipes the view and can't be undone**.
- **On-call:** create an "On Call" role, assign it, schedule it like any shift.

### Smart tools

| Tool | What it does | Needs |
| --- | --- | --- |
| [Auto-scheduler (beta)](https://kb.7shifts.com/hc/en-us/articles/4417505384979-Auto-scheduler-Beta) | Copy → Auto-schedule on a blank week; slider from labor target to full coverage; one department at a time | 6 weeks of sales (35+ days with $100+ and 50+ receipts) and 6 weeks of schedules; gradual rollout |
| [Quick Fix](https://kb.7shifts.com/hc/en-us/articles/5130561132819-Quick-Fix-Scheduling) | Warnings → "Fix them for me" reassigns conflicted, OT or unassigned shifts | Employees who hold the role |
| [Labor Budget](https://kb.7shifts.com/hc/en-us/articles/4417514442771-Use-the-Labor-Budget-Tool-to-plan-labor-costs) | Bottom of Schedule; target by labor %, SPLH, hours or IPLH | POS sales, manual import or typed sales |
| [Optimal Labor](https://kb.7shifts.com/hc/en-us/articles/4417514452115-The-Optimal-Labor-Tool) | Hourly staffing recommendation; green within 5% | POS, 15 weeks of sales and schedules, 50+ receipts/day |
| Manual sales import | Schedule → Tools → Import Sales (15-minute to hourly net sales, up to 18 months) | Locations without a POS; re-upload replaces dates |
| Weather | 16-day forecast on Schedule and Log Book | Location street address |

### Publish

**Publish Schedule** → choose departments, roles, days → notify only those with changes, everyone, or no one. Only employees who accepted their invite get notified. Editing a shift returns it to draft; republish to push changes. Editing a shift that's in the Shift Pool pulls it from the pool on publish. Warnings (exceptions, overtime, conflicts, unassigned, minors) **never block publishing** and only appear once an Admin sets the compliance jurisdiction. [Publishing](https://kb.7shifts.com/hc/en-us/articles/4417514210067-How-to-publish-a-schedule)

### Shift pool, trades, open shifts

- **Turn on:** Settings → Company Settings → Shift Pool — manager approval, partial shifts, account-wide pickups (cross-location pickups record at $0 wage unless the role is assigned).
- **Approve:** Schedule → Shift Pool (needs "Can manage schedules" and "Can approve/decline shift pool requests").
- The employee who offered a shift **stays responsible** until someone claims it and a manager approves.
- **Trades** only between the same location, department and role; they cancel if either shift is republished.
- **Open shifts:** grey row in List layout; staff bid, manager assigns. Expired ones linger until removed.
- **Sick call-outs:** "Find cover" from 24 hours before the shift; otherwise Shift+drag to someone else and flag the original Sick or No-show.

### Availability and time off

- **Availability:** Schedule → Availability. Recurring rules plus temporary overrides; can't add for a week already started. **Declining an updated recurring request wipes the old rules**, leaving the person fully available. One profile covers all locations.
- **Time off:** Schedule → Time Off. Managers can't approve another Manager's request. Blocked Days stop new requests (not sick PTO). PTO policies (Company Settings → Time Off) accrue from Jan 1 and sync balances to 7shifts Payroll, ADP, QBO, Gusto, Paychex and others.

## Time clocking and timesheets

Punches come from 7punches (7shifts' own clock app, on all paid plans) or from the POS; either way, **only approved punches in a closed timesheet reach reports, exports and payroll**. Unapproved or disputed punches are the most common reason hours go missing.

### Set up 7punches

1. Settings → Add-ons → 7punches → Enable.
2. Create the pay schedule (first day of the first period + frequency) and pick 7punches or POS as the time source.
3. Install 7punches on a tablet (iOS, Android or Amazon) and log in as an Admin, Manager or Assistant Manager. Only the person who launched the pad can change its location or log it out.
4. Punch IDs auto-assign (4 digits) only on the first enable; afterwards set them under Team → employee → Employment (up to 12 digits, unique). Bulk-email IDs from Team → Bulk Actions. [7punches setup](https://kb.7shifts.com/hc/en-us/articles/4417514077587)

### Key settings (Time Clocking → Settings; save each section)

| Setting | What it does | Watch out |
| --- | --- | --- |
| Mobile Punch | Clock in from a personal phone | Doesn't check location on its own — pair with Geofencing |
| Geofencing | Requires being within \~25–500 m of the location | Account-wide; checked only at the moment of punching |
| Buddy Punch prevention | Photo at each punch | Staff who deny camera access can still punch; no biometrics |
| Grace period + auto-approve | Approves punches near the scheduled time | Not applied to exceptions, missed enforced breaks or auto punch-outs |
| Prevent early punch-in | Blocks clocking in before the shift | 7punches only; set POS limits in the POS |
| Allow punch without shift | Fixes "Punch failed" for unscheduled staff | Applies everywhere and ignores role assignments |
| Breaks | Paid, unpaid or enforced breaks triggered by shift length; alert at 75% of threshold | Enforced breaks apply only to scheduled shifts |
| Attestation | At clock-out, staff confirm breaks taken, waived or prevented (English/Spanish) | Offline punches skip it; managers can't edit answers |
| Declare tips | Staff enter cash tips at clock-out | Needed for tip pools when 7punches is the clock |
| Edit POS punches in 7shifts | Correct POS punches here | A punch deleted here can re-sync unless also deleted in the POS |

A missing clock-out defaults to midnight UTC — fix it before payroll or you'll overpay. [Time clocking settings](https://kb.7shifts.com/hc/en-us/articles/32163860883347)

### Approve → close → export

1. **Review:** Time Clocking → Review (beside the pay period) → location → Employee Punches. **Approve All only covers the current page.** POS punches arrive approved; 7punches punches need approval. Manual edits are auto-approved; deletes are permanent; a punch's location can't be changed (delete and re-add).
2. **Disputes:** with employee approval on, staff can dispute on mobile — a dispute resets the punch to unapproved **even in a closed timesheet**, dropping it from exports. Recheck before every export.
3. **Close:** Review → Close location timesheet (all punches approved; each location closed separately). Status runs In Progress → Ready for Review → Ready for Payroll → Closed.
4. **Export:** Review → Export (payroll formats) or Pay Periods → ⋯ → Excel/CSV. Reopening after export means exporting again.

Overnight shifts count in the period they start. Changing the pay schedule affects future periods only and can trigger tip recalculation. [Review and approve](https://kb.7shifts.com/hc/en-us/articles/34232148227987)

## Labor compliance

Compliance starts with one setting: an Admin picks the **jurisdiction** under Settings → Company Settings → Labor & Compliance, which loads that state's or city's overtime, break and premium-pay rules. Until it's set, the schedule shows no compliance warnings. Individual locations can override it (location → Labor & Compliance → Use location-specific settings); "Re-sync labor & compliance settings" restores defaults.

### Overtime

- **Rule types:** daily (e.g. 1.5× after 8 hours, 2× after 12), weekly (e.g. 40 hours), consecutive days. Options to combine hours across locations, use a blended regular rate across roles, and count holidays.
- Weekly OT follows the **Start Week On** setting, not the pay period.
- **Changing OT settings recalculates past reports** and overwrites manual exception edits (payroll already run is untouched) — download reports first.
- **Alerts:** push alerts with a buffer; the mobile app shows yellow two hours before OT and red once in it (not with Heartland, Lightspeed, Upserve or Future POS).
- **Qualified OT** columns in Worked Hours & Wages support the new federal OT tax reporting (US). [Overtime](https://kb.7shifts.com/hc/en-us/articles/4417504930963-Labor-Overtime)

### Premiums and exceptions

When a shift breaks a rule (missed meal break, clopen, split shift, late schedule change), 7shifts logs a labor exception with its premium; these feed the Labor Exceptions report and, with Compliance fees on, labor cost. Examples of what's built in: California meal/rest penalties and split-shift pay; New York spread-of-hours, clopen and NYC predictability pay; Chicago, Seattle, Oregon and Philadelphia fair-workweek notice and premiums; Canadian reporting pay. OT, split shift, clopen and late-change exceptions can't be dismissed. [Exception costs](https://kb.7shifts.com/hc/en-us/articles/4417519539091)

For Basic City: Virginia pays overtime on weekly hours over 40 only, with no daily overtime ([Keka](https://www.keka.com/us/compliance/overtime-laws/virginia)), and it isn't among the fair-workweek jurisdictions 7shifts lists. So the rules that matter are weekly OT, minors and whatever break policy you set — confirm the jurisdiction reads Virginia.

### Minors (US)

Turn on under Labor & Compliance → Minors and create a **school calendar** (required, web-only, can't be deleted). Minors get an "M" icon by birth date. Ages 14–15 follow federal limits unless the state is stricter: 3 hours on school days, 8 on non-school days, 18 per school week, 7 AM–7 PM (9 PM from June 1 to Labor Day). Ages 16–17 follow state rules only. Warnings flag but don't block publishing. [Minors](https://kb.7shifts.com/hc/en-us/articles/50666495915667)

### Wages and tip credit

Turn on **wage-based roles** before entering role wages, or the rates won't show in reports. Tip credit tracking needs a POS tip source, is US-only (not AK, CA, MN, MT, NV, OR, WA), and checks shortfalls weekly. [Tip credit](https://kb.7shifts.com/hc/en-us/articles/18430941616915)

## Payroll and tips

7shifts Payroll is US-only, run by Admins on the web, and processed by Check Technologies; every location in one payroll must share the same FEIN and bank account. Tips flow in from the POS (or manual entry), get pooled and calculated, then go to payroll or straight to employees' debit cards through Tip Payouts.

### 7shifts Payroll setup

Payroll → Get started, then the **Switcher** tab (moving from another provider) or **Inception** (new):

1. Business details and a signatory.
2. Locations — only those under the same EIN (others need the Multi-EIN workflow).
3. Business checking account via Plaid or manual ACH (no personal accounts).
4. Tax IDs: FEIN, state unemployment (SUI) number and rate, state withholding number.
5. Upload prior-provider reports: demographics, direct deposit, withholdings, garnishments, pay history.
6. Submit and pick the first pay date.
7. Send employee onboarding packages — **only after migration is complete**, or you'll resend them. Onboard yourself too (Payroll → Settings → Employees → your profile).
8. Add terminated employees, then grant Check **TPA access** in each state tax portal.

Not supported: 401(k)/IRA transfers, workers' comp transfers, owner's draw, pay cards. [Setup guide](https://kb.7shifts.com/hc/en-us/articles/15313431075219)

### Running payroll

**Before each run:** every employee "Ready for Payroll"; pay period start matches Time Clocking's week start; every punch has a role and every role a wage with an effective date; multi-location staff have a primary location; POS roles and employees mapped; tip calculation set; PTO policies assigned. [Checklist](https://kb.7shifts.com/hc/en-us/articles/24283832436627)

**Run:** Payroll → Run Payroll → check period, submission deadline and pay day → review Hours & Earnings (auto-filled from time clocking; tips, bonuses, reimbursements editable) → Time Off → Preview → Submit. [Run payroll](https://kb.7shifts.com/hc/en-us/articles/15431730396179)

- **Fixing hours:** edit the timesheet, then Drafts → **Sync timesheets & tips** — this overwrites manual draft edits.
- **Bonuses:** use an off-cycle run for the flat supplemental tax rate.
- **Timing:** new accounts are on 3-business-day processing; after 10 clean funded payrolls you can qualify for 2-day.
- **Pay schedule** changes (Time Clocking → ⋯ → Edit pay schedule) apply to future periods only; once a payroll has run, frequency changes go through Support.
- **Taxes tab:** forms (941, 940, W-2, 1099, state), payment and deposit status. About a month before each quarter end (Feb, May, Aug, Nov), check TPA access, replace any "Applied For" tax IDs, and make sure every employee has finished onboarding — a missing tax ID can bounce the tax funds back to you to pay yourself. [Quarterly checklist](https://kb.7shifts.com/hc/en-us/articles/44076887665555-Quarterly-Payroll-Filing-Checklist)

| Payroll fee | Amount |
| --- | --- |
| Per employee paid | $6/month |
| Amended tax return | $175 |
| Late filing/refile from employer error | $200 per attempt |
| Failed employer funding (NSF) | $125 |
| Payment reversal attempt | $75 |
| Failed employee validation/payment; wire funding | $25 each |
| Rush pay (2-day accounts) | $0.65 per employee per run |
| Mailed W-2/1099 | $3 each (digital free) |

Billed the 1st of the next month. [Fees](https://kb.7shifts.com/hc/en-us/articles/20411025319059)

### Exporting to another payroll provider

Map employees and roles (Apps & Integrations → Mapping) → approve all punches → an Admin **closes the timesheet** → Time Clocking → Export → provider. Only approved punches in closed timesheets export. [Payroll partners](https://kb.7shifts.com/hc/en-us/articles/4417514397587)

| Provider | Type | What to know |
| --- | --- | --- |
| [Gusto](https://kb.7shifts.com/hc/en-us/articles/4417519669267) | Integration | Syncs regular/OT/double-OT hours, time off, tips (supported POS only). **Wages don't sync**; hours not split by role; remap every new hire |
| [ADP Run](https://kb.7shifts.com/hc/en-us/articles/4417514267411) | Integration (\~$15/location/month from ADP) | Connect only after ADP is live. All OT exports as non-qualified OT — fix manually where required (CA, NV). Export final hours before deactivating anyone |
| ADP Workforce Now / Next Gen | Integration | Matches employee data both ways; company and department codes |
| [QuickBooks Online](https://kb.7shifts.com/hc/en-us/articles/4417514163859) | Time export | Hours + PTO; no role split, no salaried/contractors; names must match exactly; re-authorize by Dec 1 |
| [QBO accounting connection](https://kb.7shifts.com/hc/en-us/articles/35682431320339) | 7shifts Payroll → journal entries | Summary entries by category mapped to your chart of accounts; unmapped categories don't post |
| [Paychex](https://kb.7shifts.com/hc/en-us/articles/4417505170963) | CSV export | Map by Paychex file number; optional role split and wage export |
| Paycom, Paycor, Paylocity, Heartland, UKG, Dayforce, Toast Payroll, others | File export | Same approve → close → export flow |

### Tip pooling

Add-on (Admins buy it under Settings → Add-ons → Tip Pooling; web-only setup). Needs Time Clocking on. Tips come automatically from Toast, Square, TouchBistro, GoTab, Revel, Qu and Lightspeed K-Series; otherwise enter daily totals manually (Tip Pooling → Add Tips, and Save). [Tip Pooling 101](https://kb.7shifts.com/hc/en-us/articles/4417505157779)

Create a pool: Tip Management → Tip Pooling → **+ Create Pool** → location, frequency (daily default), optional day part → contributors by role/department as % of net sales or % of tips → POS tip fields (credit card, cash, auto-gratuity, declared) → distribution → receivers.

| Distribution | How it splits |
| --- | --- |
| Equal | By hours worked across everyone ($500 ÷ 25 hours = $20/hour) |
| Percentage | Fixed share per role group (must total 100%), then by hours within the group |
| Points | Weighted points per role or department — check the Live Preview |

- A role that didn't work that day has its share redistributed proportionally to roles that did.
- Rule changes recalculate the **current open period** retroactively; someone in two pools can be double-counted.
- Totals take about 30 minutes to refresh in reports.

**Tip calculation** (Tip Management → Settings, Admin, per location) tells payroll which sources count and whether each is Paid or Deducted. New locations copy the last setup — review it. [Tip calculation](https://kb.7shifts.com/hc/en-us/articles/17638032595987)

### Tip Payouts

Pays tips to employees' debit cards through the 7shifts app. Requires the contiguous US, 2-step verification, and employees with a debit card and the app. Setup order: tip calculation → payout batch → fees (restaurant, employee or shared) → connect bank → first batch → float. Funds take up to 5 business days. In 7shifts Payroll, paid-out tips show in a "Tips Already Paid" column so they're taxed; **Override** pays exactly what you type and zeroes the amount owed. [Payouts setup](https://kb.7shifts.com/hc/en-us/articles/13512748231699)

## Team communication, engagement and HR

Since Jan 15, 2026 the free Comp plan has lost team chat, hiring, engagement tools and 7punches (it keeps one-way announcements and is capped at 15 employees), so everything in this section assumes a paid plan. [Comp changes](https://www.7shifts.com/comp-pricing-changes-2026/)

| Tool | Where | What to know |
| --- | --- | --- |
| [Messaging](https://kb.7shifts.com/hc/en-us/articles/4417513445011) | Chat bubble (top right on web; chat icon on mobile) | Location, department and role channels fill automatically from assignments — change the assignment to remove someone. Shift chats reach whoever is scheduled on a date (published shifts only). DMs and custom groups have no size cap; new members see full history |
| [Announcements](https://kb.7shifts.com/hc/en-us/articles/34012568695955-How-to-View-Announcements) | Chat → Announcements tab | One-way posts to a location, department or role, with attachments. Not on the trial |
| [Shift Feedback](https://kb.7shifts.com/hc/en-us/articles/4417519785875) | Settings → Locations/Departments/Roles → edit location → Shift Feedback | Prompt goes out 1–60 minutes around shift end; 48 hours to answer. Fixed questions, and **can't be turned off once enabled**. Results in Log Book, Engage and the Shift Feedback report |
| [Engage dashboard](https://kb.7shifts.com/hc/en-us/articles/34666446631955) | Dashboard or Team | Lates, no-shows, dropped shifts, shift score, tenure; exportable; managers see only their locations |
| [Manager Log Book](https://kb.7shifts.com/hc/en-us/articles/4417520176531) | Log Book (clipboard icon on mobile) | Admins, managers, assistant managers only. Admins set categories; POS or 7punches auto-adds sales and labor. Day rolls at 12:01 AM, so back-date late-night notes. Each user turns on their own notifications |
| [Tasks (7tasks)](https://kb.7shifts.com/hc/en-us/articles/4417520173075) | Tasks (web to build lists) | Paid add-on. One-time or recurring lists per location; completion by checkbox, photo, value or **temperature** (useful as food-safety logs); reminders at shift start and alerts for incomplete lists |
| [Hiring](https://kb.7shifts.com/hc/en-us/articles/4417514332691-Hiring) | Settings → Add-ons → Enable Hiring | Post from 18 generic roles; indexed on Google and Jobcase. Moving a candidate to Hired creates their profile and sends onboarding. No resume uploads; hiring doesn't close the posting; status changes don't email candidates |
| [Onboarding](https://kb.7shifts.com/hc/en-us/articles/5804548839699) | Team → Onboarding (web) | US only. I-9, W-4, personal info, payment method, state/local forms by location address, plus your own docs. Included on some plans, otherwise $2.99 per package; free with Toast. Upload state notices (e.g. California's 2810.5) yourself |

**Onboarding package won't send?** The employee needs an active employment period (profile → Employment tab), and the location needs a valid address (Settings → Locations → General). Managers need five permissions: add employees, edit employees, manage documents, send/approve onboarding, view wages. [Fix](https://kb.7shifts.com/hc/en-us/articles/41952407727251)

## Reports and analytics

Reports live under **Reports** in the left nav; managers need the "Can run reports" permission, and every labor report needs either 7punches or a POS integration with Actual Labor on. Star a report to pin it (saved per user and device). [Reports overview](https://kb.7shifts.com/hc/en-us/articles/35619471025811)

| Report | Use it for | Gotchas |
| --- | --- | --- |
| [Worked Hours & Wages](https://kb.7shifts.com/hc/en-us/articles/4417519622547) | Actual hours and pay by employee, role, location; tick Include Tips | **Approved punches only** — a blank report usually means unapproved punches or blocked pop-ups. Can differ from 7shifts Payroll, which calculates OT itself |
| Scheduled Hours & Wages | Planned labor before the week starts |  |
| [Variance](https://kb.7shifts.com/hc/en-us/articles/4417513558675) | Scheduled vs actual hours and wages | Also from Time Clocking → ⋯ → Variance; CSV export |
| [Actuals](https://kb.7shifts.com/hc/en-us/articles/4417504768147) | Net sales and labor by day, week or month | Department filter needs department-based budgeting |
| [Labor Summary](https://kb.7shifts.com/hc/en-us/articles/33019937675539-Labor-Summary) | Hours and wages per pay period | Time Clocking → Review pay period → Labor Summary; unapproved punches excluded |
| Weekly Overtime, Labor Exceptions, Attendance, Punch Audit | Catching OT, missed breaks, lates, punch edits |  |
| Payroll Summary/Journal, Tip Payouts | Payroll and tip reconciliation |  |
| Time Off, Shift Pool, Shift Feedback, Tasks, Certifications, Activity Log | Operational audits |  |

Live **sales vs labor** shows on the Dashboard when a POS with Actual Labor is connected. For analysis outside 7shifts, the API's Daily Sales & Labor and Hours & Wages reports return the same figures (see the API section).

## Integrations

Map employees and roles one-to-one **before** turning on Employee Sync, Actual Labor or Wage Sync, and make the POS and 7shifts agree on time zone and a 5 AM–5 AM business day. Up to 90 days of sales history imports; forecasts need 1–2 weeks of data. Setup is web-only and needs "Can manage integrations." [Integrate your POS](https://kb.7shifts.com/hc/en-us/articles/26782471136147)

| POS | Sales | Punches | Tips | Employees | Wages | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| [Toast](https://kb.7shifts.com/hc/en-us/articles/4417514408595) | Net sales (service charges optional) | Yes | CC, auto-grat, cash, declared | Names, status, location; email/ID only at creation; roles set manually | Hourly only (needs wage-based roles) | Toast charges \~$50/location/month. Set tips up after the first sync |
| [Square](https://kb.7shifts.com/hc/en-us/articles/4417519588243) | About every minute | Yes | Declared (turn on cash-tip declaring in Square) | Yes, after mapping | Not stated | Punches get a role only if the employee was scheduled |
| [Lightspeed K-Series](https://kb.7shifts.com/hc/en-us/articles/12924029984787) | Net sales | Yes | CC, auto-grat | New employees | No (roles neither) | Accept every permission scope; pre-Sep 2024 connections must reconnect |
| [Clover](https://kb.7shifts.com/hc/en-us/articles/4417519577235) | Yes | No — use 7punches on the Clover tablet | — | — | — | Token expires about yearly: re-authorize from Clover's app market |

**Connecting Toast:** 7shifts → Apps & Integrations → Toast → Start Setup → in Toast, Integrations → 7shifts → gear icon → paste the 7shifts **Partner Group ID** (leave Location IDs blank) → back in 7shifts, Check Connection → pick locations, sales and labor options → map jobs to roles → import employees → Finish. Repeat per location. If it won't connect, try the Restaurant GUID from Toast's partner email.

**Toast employee not syncing?** Their job isn't mapped, they have no email (with schedule enforcement on), they're stuck on the mapping page, or the hourly sync hasn't run. **Declared tips missing?** Toast declared tips only come with Toast punches — if staff clock in on 7punches, turn on Declared Tips in Time Clocking → Settings instead. [Toast sync fixes](https://kb.7shifts.com/hc/en-us/articles/35835504508691)

**Other connections:** payroll providers (Payroll and tips section), QuickBooks Online, ADP, plus apps listed under Apps & Integrations — 7shifts advertises 70+ integrations across POS, payroll and HR, and Clair on-demand pay (free for employers).

## 7shifts API

For Basic City's own data, an **access token** from **Settings → Developer Tools** ([app.7shifts.com/settings/developer\_tools](https://app.7shifts.com/settings/developer_tools)) is all you need: it's REST at `https://api.7shifts.com/v2`, Bearer auth, version header `2026-01-01`. OAuth is only for vetted partners selling to many 7shifts customers. Researched from [developers.7shifts.com](https://developers.7shifts.com/docs/) on Nov 8, 2026.

### Authentication

|  | Access token | OAuth client |
| --- | --- | --- |
| Who | Any company admin, self-serve, no approval | Vetted partners only, after review and certification ([become a partner](https://www.7shifts.com/become-a-partner)) |
| Reach | Admin-level access to one company; cannot be scoped down | Scopes in read/write pairs: companies, departments, locations, roles, users, sales, shifts, time\_punches, events |
| Lifetime | Long-lived; auto-deleted after 6 months with no activity | 1-hour tokens from `POST https://app.7shifts.com/oauth2/token` (client credentials); no refresh token |
| Headers | `Authorization: Bearer <token>` | Bearer token **plus** `x-company-guid: <guid>` |

Creating a token: Developer Tools → Access Tokens → Create Access Token. Name it for its use (e.g. "Toast sync"), assign a technical contact who is an active admin, and optionally set its default API version. Deleting is immediate (type `DELETE` to confirm). Since **Oct 31, 2026**, every token or grant reaches only the company it was made for. Keep tokens server-side — there is no sandbox, so a token can read wages and personnel data. [Authentication guide](https://developers.7shifts.com/docs/authentication)

### Conventions

- **Headers:** `Authorization`, `x-api-version: 2026-01-01` (send it on every call; without it the token's default version applies), `Content-Type: application/json` on writes.
- **Versions:** 2026-01-01 current; 2025-03-01 and 2023-04-01 still supported. Each version lives at least 12 months; webhooks aren't versioned. [Versioning](https://developers.7shifts.com/reference/versioning)
- **Company ID:** `GET /v2/companies` → `id`. Almost every path is `/v2/company/{company_id}/...`.
- **Money** is integer cents; **datetimes** are UTC ISO 8601; many filters take `YYYY-MM-DD`. Each location carries its IANA time zone.
- **Pagination** is cursor-based: send `limit` + `cursor`, follow `meta.cursor.next` until null. Limits vary by endpoint (general page says 20 default/200 max; users, locations and shifts allow up to 500). [Pagination](https://developers.7shifts.com/reference/pagination)
- **Rate limit:** 600 requests/minute (overage blocks you for a minute); payroll and orders guides also say 10 requests/second per token. Back off on `429`.
- **Errors:** 401 re-auth; 403 no access **or the company's plan is too low for that endpoint**; 404 usually a stale mapping; 422 validation; newer endpoints return `application/problem+json`. [Errors](https://developers.7shifts.com/reference/errors)

```bash
curl https://api.7shifts.com/v2/companies \
  -H "Authorization: Bearer $TOKEN" -H "x-api-version: 2026-01-01"

curl "https://api.7shifts.com/v2/company/$CID/time_punches?clocked_in[gte]=2026-11-01&clocked_in[lte]=2026-11-07&approved=true&limit=100" \
  -H "Authorization: Bearer $TOKEN" -H "x-api-version: 2026-01-01"
```

### Key endpoints

Paths are under `https://api.7shifts.com`; `{cid}` = company ID. Plan = minimum plan the reference names (legacy names: Entrée ≈ Essentials, The Works ≈ Pro, Gourmet ≈ Premium).

| Area | Endpoint | Notes |
| --- | --- | --- |
| Identity | `GET /v2/whoami` | Confirms who the credential is |
| Companies | `GET /v2/companies` | `id`, `name`, `status`, `plan_id` |
| Locations | `GET /v2/company/{cid}/locations` | Full CRUD |
| Departments | `GET /v2/company/{cid}/departments` | Filter by `location_id`; full CRUD |
| Roles | `GET /v2/company/{cid}/roles` | A role with no department has `department_id = 0` |
| Users | `GET /v2/company/{cid}/users` | Filter `status`, `location_id`/`department_id`/`role_id`, `name`, `modified_since`; `DELETE` deactivates. Plan: Entrée |
| Wages | `GET /v2/company/{cid}/users/{user_id}/wages` | `current_wages[]`, `upcoming_wages[]`; `hourly` or `weekly_salary` in cents |
| Shifts | `GET /v2/company/{cid}/shifts` | Filters `start[gte]`/`start[lte]`, `location_id`, `user_id`, `draft`, `deleted`, `open`; CRUD. Plan: The Works |
| Scheduled check | `GET /v2/company/{cid}/shifts_scheduled/{user_id}` | `?grace_period=0..60`; used to enforce schedules at clock-in |
| Time punches | `GET/POST/PUT/DELETE /v2/company/{cid}/time_punches` | Filters by dates, `approved`, `flags` (late, auto\_clocked\_out, missed\_break), `business_date_start/end`. A `PUT` must resend every existing break or they're deleted. Plan: Entrée |
| Payroll periods | `GET /v2/time_clocking/payroll_periods?company_id=` | Add `include_punches_status=true` |
| Hours & wages report | `GET /v2/reports/hours_and_wages` | Requires `company_id`, `from`, `to`, `punches`; 7shifts-calculated regular/OT/holiday hours and tips; 30-second timeout |
| Sales & labor report | `GET /v2/reports/daily_sales_and_labor` | `start_date`, `end_date`, `location_id`; actual vs projected sales and labor, SPLH, labor % |
| Orders (sales in) | `PUT /api/v2/company/{cid}/location/{lid}/orders/{external_id}` | Idempotent upsert; replaces `/receipts` (deprecated, no sunset date) |
| Time off | `GET /v2/time_off?company_id=` | Approve/decline endpoints too. Plan: The Works |
| Availability | `GET /v2/company/{cid}/availabilities` | Status 0 pending, 1 approved, 2 declined |
| Events | `GET /v2/company/{cid}/events` | Birthdays, work anniversaries, general |
| POS mappings | `GET /v2/company/{cid}/external_user_mappings` | Links POS employee IDs to 7shifts users |

Also documented: role assignments, employment records, log book (beta), tasks, forecast overrides, tip pool settings and reports, day parts, shift feedback. Map people on the user `id`, not `employee_id` or `punch_id`. [Glossary](https://developers.7shifts.com/docs/glossary) · [Mapping](https://developers.7shifts.com/docs/mapping)

### Webhooks

Set up in **Developer Tools → Webhooks** (admin, Premium/Gourmet plan) by picking an event and a URL, with a Test button; or via `POST /v2/company/{cid}/webhooks` with `{"url","method":"post","event"}`. Events: `schedule.published`, `payroll_period.closed`, `time_punch.created/edited/deleted`, `user.created/modified/deactivated/reactivated`, and created/modified/deleted for `department`, `location`, `role`. There are **no shift-level events** — use `schedule.published`. Treat each webhook as a trigger and re-fetch the record: no payload schema or retry policy is published, and HMAC signatures (`x-hmac-signature`) come only on partner-configured webhooks. [Webhooks](https://developers.7shifts.com/reference/webhooks-introduction)

### Recipes

1. **Payroll / labor export.** On `payroll_period.closed`, read payroll periods and export only when finalized and all punches approved. Either pull approved time punches (hours = out − in − unpaid breaks) plus wages, or call the Hours & Wages report per location for 1–2 weeks at a time. Re-read a trailing window — managers edit punches after close. [Hours & wages guide](https://developers.7shifts.com/docs/hours-wages-report)
2. **Schedules to a warehouse.** For each date window, pull shifts three times — published, `deleted=true`, `draft=true` (never combine the last two) — upsert on shift `id`, keep drafts separate, re-sync on `schedule.published`. [Shift sync guide](https://developers.7shifts.com/docs/synchronizing-shifts-for-data-warehousing)
3. **Pushing sales (custom POS).** Upsert revenue centers, sales categories and dining options, then `PUT` each order by its external ID with totals in cents. The first order sent fixes the location's cutover date permanently, so backfill 90+ days oldest-first. Not needed when Toast or another certified POS integration is connected. [POS guide](https://developers.7shifts.com/docs/integrating-a-pos)

### Tools for building

- **7shifts MCP server** at `https://developers.7shifts.com/mcp` lets an AI assistant browse the spec and run GET calls with your token. [MCP server](https://developers.7shifts.com/docs/mcp-server)
- **Embed SDK** (partners): drop-in sales-and-labor and who's-working widgets.
- **Status:** [status.7shifts.com](https://status.7shifts.com/) · **Questions:** [developer discussions](https://developers.7shifts.com/discuss) · [Changelog](https://developers.7shifts.com/changelog).

| Date | Change |
| --- | --- |
| Oct 31, 2026 | Credentials limited to a single company |
| Fall 2026 (v2.2026.1001) | Orders API and MCP server added; inactive tokens removed after 6 months |
| Jul 2, 2025 | Plan names changed |
| 2025-03-01 | API version with employment records, contacts, role-assignment endpoints |
| May 31, 2023 | V1 endpoints end of life |
| Mar 31, 2023 | API keys end of life (use access tokens) |

**Open questions in the docs:** some Orders examples use `/api/v2` instead of `/v2`; webhook signature encoding is unspecified; a few create/update/delete paths are inferred from reference page names — test before relying on them.

## Troubleshooting and FAQ

Most problems trace back to three things: unapproved punches, unmapped employees or roles, and an expired integration token.

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Employee can't log in | Wrong password, expired reset, browser autofill | "Forgot password?" (link lasts 24 hours), or Team → Employees → profile → Personal → Send password reset (once per 3 minutes). Clear autofill and type it in. Passwords need 8+ characters with upper, lower and a number. [Login help](https://kb.7shifts.com/hc/en-us/articles/24142605221779) |
| Invite link doesn't work | Invites expire after 5 days | Team → Invite Employees resends to everyone pending, or resend from the profile. Already accepted → sign in or reset. [Invites](https://kb.7shifts.com/hc/en-us/articles/45486692632083-Handling-expired-or-non-working-invite-links) |
| No push notifications | App or phone setting off | App ☰ → Notification Settings → Push on; on iPhone also allow in iOS Settings; toggle off and on. [Notifications](https://kb.7shifts.com/hc/en-us/articles/15981314768147) |
| "No role" punches; payroll blocked | Unmapped POS employee or job | Time Clocking → Pay Periods → **Warnings** tab: map or ignore the employee, map the role, or set the role on the punch. Editing old schedules won't fix it. [Warnings](https://kb.7shifts.com/hc/en-us/articles/46677260427923) |
| Hours missing in payroll | Unmapped employees or roles | Integrations → Mapping, filter by mapping status |
| Worked Hours report blank | Unapproved punches or pop-up blocker | Approve punches; allow pop-ups |
| Shift or punch missing from labor reports or tip pools | Shift ended after the location's operating hours | Extend hours in Settings → Locations and rerun. Never delete the shift. [Fix](https://kb.7shifts.com/hc/en-us/articles/48476655673875-Why-are-shifts-or-punches-missing-from-Labor-Reports-and-Tip-Pools) |
| Schedule not reaching the POS (enforcement) | Red Shift Sync icon on the Schedule page | Hover for the reason: enforcement off in POS, wrong time zone, job codes or PINs don't match, shift published after it started, incomplete mapping. [Punch enforcement](https://kb.7shifts.com/hc/en-us/articles/4417513982739-Common-Issues-for-Punch-Enforcement-Sync) |
| Clover sales stopped | Annual token expiry | Re-open 7shifts in Clover's app market; if not, disconnect and reconnect |
| Lightspeed red banner | Authorization lapsed | Re-authorize; reconnect if connected before Sep 2024 |
| API returns 403 | Plan too low for that endpoint, or wrong company | Check the plan requirement; since Oct 31, 2026 a token only reaches its own company |
| API 404 on a known record | Stale mapping or deleted record | Re-read the list endpoint with `deleted=true` and remap |

**Getting help:** in-app chat (Help, bottom left on web; More → 7helps on mobile) — no queue since Sep 2024; you get an email when an agent replies. Hours Mon–Fri 9 AM–7 PM ET, Sat–Sun 11 AM–7 PM ET. Phone 1-888-979-5877. [Contact us](https://kb.7shifts.com/hc/en-us/articles/40672939862163)

## Sources

Article links sit beside each section; these are the main entry points. Not verified: plan-by-plan feature lists and add-on prices (third-party), how auto punch-out and rounding are configured, webhook payload schemas and signature encoding.

- [7shifts Knowledge Base](https://kb.7shifts.com/hc/en-us) — [Getting Started](https://kb.7shifts.com/hc/en-us/categories/4426859309587-Getting-Started), [Reports](https://kb.7shifts.com/hc/en-us/sections/360003909413-Reports), [Payroll integrations](https://kb.7shifts.com/hc/en-us/sections/5111728535443-Payroll-Integrations), [Contact us](https://kb.7shifts.com/hc/en-us/articles/40672939862163)
- [7shifts developer docs](https://developers.7shifts.com/docs/) — [Authentication](https://developers.7shifts.com/docs/authentication), [Versioning](https://developers.7shifts.com/reference/versioning), [Pagination](https://developers.7shifts.com/reference/pagination), [Errors](https://developers.7shifts.com/reference/errors), [Webhooks](https://developers.7shifts.com/reference/webhooks-introduction), [Mapping](https://developers.7shifts.com/docs/mapping), [Changelog](https://developers.7shifts.com/changelog), [MCP server](https://developers.7shifts.com/docs/mcp-server)
- [7shifts Comp plan changes 2026](https://www.7shifts.com/comp-pricing-changes-2026/)
- Pricing (third-party): [Connecteam review](https://connecteam.com/reviews/7shifts/), [CostBench](https://costbench.com/software/employee-scheduling/7shifts/), [HiveDesk review](https://www.hivedesk.com/7shifts-review)
- Virginia overtime: [Keka](https://www.keka.com/us/compliance/overtime-laws/virginia)
