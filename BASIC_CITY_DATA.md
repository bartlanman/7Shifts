# Basic City in 7shifts: IDs, data feeds and labor numbers

Nov 27, 2026 · companion to `README.md`, which covers how 7shifts works in general.

## Account
- **Company:** 293422.
- **Location:** 362530 (Basic City Beer Co.), on the Premium plan.
- **Logins:** Bart's user 7139840 is an admin. Clock-ins happen on **Toast**; the punches flow into 7shifts with `pos_type = toast`. Payroll runs in **QuickBooks**, not 7shifts.

## Departments

| Department | ID | Counts as |
|---|---|---|
| Bartender | 544738 | Front of house. Also holds the Server, Host, Foundry Bartender, Showroom Bartender and Barista roles. |
| Barback | 820300 | Front of house |
| Kitchen | 544740 | Kitchen. Roles: Pizza, Expo, Lunch, GRILL, FLOATER. |
| Sip City | 771885 | Front of house in the recap model (row 69 includes Sip City) |
| Production | 585562 | Brewery |
| Foundry | 585564 | Foundry (includes Janitorial) |

## Where the data is
- **Supabase (Foundry Data, schema `sevenshifts`):** synced daily. Use this first.
  - `shifts`: published schedule, from Jan 2025.
  - `time_punches`: Toast clock-ins, from Mar 26, 2026.
  - `wages`, `users`, `roles`, `departments`.
  - `v_schedule_vs_actual`: each shift, scheduled vs worked hours, late in, past end.
  - `v_unscheduled_punches`: punches with no matching shift.
- **Live API (when Supabase is stale):** from a logged-in app.7shifts.com tab, call `fetch('/api/v2/company/293422/...')`. Useful paths: `users?name=`, `users/{id}/wages`, `shifts?start[gte]=`, `time_punches?clocked_in[gte]=`, `roles`, `departments`. Paging uses `meta.cursor.next`.
- **Gotchas:**
  - `shifts.hourly_wage` is 0, so take pay rates from the wages endpoint or table.
  - Bartender rates ($8–9) are tipped wages.
  - About 7 of 37 scheduled people have no wage on file. For them, use the role median.

## Labor numbers used in the recap plan (Nov 27, 2026)
Wages only, before payroll tax and tips. Built from the schedule at current pay.
- **Minimum crew, front of house including Sip City:** $7,500 a month.
  - Lightest month (Feb 2026): bar about $4,950 for 605 hrs.
  - Sip City about $2,200 at Sep–Nov 2026 staffing.
  - Plus about 5% for actual clock-ins over the schedule.
- **Minimum crew, kitchen:** $7,600 a month.
  - Lightest month (Feb 2026): about $7,100 for 376 hrs.
  - Plus about $165 for Riley's raise and about 4% for actual clock-ins.
- **Clock-ins vs schedule, Apr–Nov 2026:**

| Department | Worked vs scheduled | Unscheduled hours a month |
|---|---|---|
| Front of house | about 102% | about 27 |
| Kitchen | about 98% | about 29 |
| Sip City | about 85% | about 34 |
| Production | n/a | about 75 |

- **Riley Saunders:**
  - $19/hr on Grill, Pizza and Floater since Nov 9, 2026.
  - A +$1/hr kitchen lead promotion is planned for Dec 2026; enter it in 7shifts and payroll when it starts.
  - Averages 38.2 hrs/wk (Apr–Nov 2026).

Full working: `bartlanman/SWITCHCANE` `evidence/capacity-and-labor-minimums-2026-11-27.md`.
