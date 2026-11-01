# Methodology

Reference date ("today" on the dashboard): 2026-10-31. All data is synthetic and seeded, so results are reproducible.

## Windows
Week, month and quarter are **trailing** windows of 7, 30 and 90 days from the reference date.

## KPI definitions
| KPI | Definition |
|---|---|
| Headcount | Employees with `hire_date <= ref` and no `term_date` on or before ref. |
| Joined (week / month / quarter) | Employees whose `hire_date` falls inside the trailing window. |
| Open roles | Requisitions with status `open` and `opened_date <= ref`. |
| Final interview rounds | Candidates currently in the Interview stage flagged `final_round = 1`. |
| Screens held | Candidates whose `screen_date` is in the trailing 7 days. |
| Roles opened (quarter) | Requisitions opened in the trailing 90 days. |
| Retention, 12 months | Cohort method. Cohort = employees employed on (ref minus 365 days). Retention = share of that cohort still employed on ref. The comparison figure repeats the calculation one year earlier. |
| Departures | Employees whose `term_date` falls inside the trailing window. |
| Payroll, monthly | Sum of base pay, benefits and equity, and employer taxes for the latest period (October 2026). Employees hired or leaving mid-month are prorated by days. |
| Payroll vs Sep / vs Q2 | October total against the September total and against the average of April to June. |
| Next run | Latest `pay_date`. Pay date is the first Friday of the month after the period. |
| Median time-to-offer | Median days from `applied_date` to `offer_date` for offers extended in the trailing 365 days (declined offers included). Computed with `ROW_NUMBER()` and `COUNT() OVER`. |
| Active candidates | Candidates in Applied, Screen, Interview or Offer. Accepted candidates who have not started yet appear in the Hired column but are not counted as active. |
| Starts in November | Accepted candidates with a `start_date` in November 2026. |

Retention uses the cohort method rather than "1 minus separations over average headcount" because it is easy to audit row by row. Time-to-fill and time-to-hire are different measures; this project reports **time-to-offer** only and names it that way.

## Synthetic assumptions (not real rules)
- Annual base salary: level 1 to 5 base of $55K, $95K, $125K, $160K, $215K, times a department factor (0.85 to 1.05) and a seeded random factor of plus or minus 5%.
- Benefits and equity: 8, 15, 20, 25 and 30% of base for levels 1 to 5.
- Employer taxes: a flat 7% of base.
- Two recent joiners (Aug and Oct 2026) and six former employees were added so the joiner, departure and retention figures have something to measure.
- Other hire dates come from the template's tenure values.

## Data-quality checks
Ten checks must return zero violations before any KPI is computed: term before hire, missing hire date, manager not found, manager hired after report, pipeline dates out of order, candidate applied before the requisition opened, future-dated candidate events, payroll for people not employed, active employee missing from the latest run, and leave after termination. The notebook then injects three errors into a copy of the data and confirms the checks fail as expected. This step caught a real error in the first draft of the data (a manager hired after their report).

## Rounding
Department payroll is shown in whole $K using largest-remainder rounding so the rows add up to the rounded total on the page.

## Differences from the Muse template
- Typed KPI values were replaced by computed ones, so some changed: retention 94% became 93.0%, headcount joiners "3 this month" became 1, active candidates 38 became 36, median time-to-offer 24 days became 34, payroll $612K became $601K.
- The "Out this week" list is now derived from the leave calendar. Sofia Petrova's parental leave in that calendar ends Nov 10, where the template's list said Dec 4.

## Known limitations
- Small samples. Some department medians rest on a single offer.
- Retention is measured on a 43-person cohort; one departure moves it by about 2.3 points.
- Trailing windows are not calendar weeks or months.
