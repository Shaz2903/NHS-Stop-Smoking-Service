# NHS Stop Smoking Services :Local Authority Performance Analysis

A West Sussex-focused analysis of NHS England's 2025/26 Stop Smoking Services data: quit rates, regional comparison, spend per quitter, and a breakdown by socio-economic group and support type. Built in Python, with a companion Power BI dashboard.

## Data source

NHS England, [Statistics on Local Stop Smoking Services in England, April 2025 to March 2026 (Q4, Annual)](https://digital.nhs.uk/data-and-information/publications/statistical/statistics-on-nhs-stop-smoking-services-in-england/april-2025-to-march-2026-q4-annual). Official statistics, published 23 July 2026. Crown copyright, Open Government Licence v3.0.

Two files are used:
- `stat-stop-smok-serv-eng-2025-26-q4.csv` — quarterly activity data by local authority, broken down by age, ethnicity, socio-economic group, and intervention type.
- `stat-stop-smok-serv-finance-eng-2025-26-q4.csv` — annual spend by region and nation.

## Data dictionary (columns used)

| Column | Meaning |
|---|---|
| `OrgType` / `OrgName` | Geography level (National / LARegion / LA) and its name |
| `Quarter` | Financial quarter within 2025/26 (1 to 4, not cumulative — each quarter is a discrete period) |
| `MonitoringCategoryType` / `MonitoringCategory` | The breakdown a row belongs to (e.g. Age band → "18-34"). Filtered to `Age band` / `All ages` to get each area's overall total and avoid double-counting |
| `Total referred` | People referred into the service |
| `Set quit date` | People who set a date to stop smoking |
| `Quit` | People who had successfully quit at 4-week follow-up (self-reported) |
| `Quit (%)` | `Quit` as a share of `Set quit date` |

**Data quality note:** small counts are suppressed by NHS England using `:` or `*` in place of a number, so all activity columns were read as text and coerced to numeric, turning suppressed cells into missing values rather than 0. A naive read of the raw CSV silently concatenates these as strings instead of summing them — this was caught and fixed during the project.

## Method

1. Filtered to `Age band` / `All persons` / `All ages` rows to get one row per area per quarter, avoiding the double-counting that comes from age-band and demographic breakdowns sitting in the same file.
2. Built a quarterly trend for West Sussex against its region (South East) and England.
3. Summed all four quarters to get an annual total per South East local authority, for a fair year-on-year peer comparison.
4. Combined annual quits with the finance file (delivery spend + cost of stop smoking aids) to get a cost-per-quitter figure.
5. Repeated the same aggregation for socio-economic group and support (intervention) type, comparing West Sussex to England on each category, and excluding categories under 30 West Sussex quit attempts as too small to read reliably.

## Findings

**West Sussex closed a slow start.** Quit rate ran below the South East average for the first two quarters of 2025/26 (44.9% and 43.5%), then rose sharply, ending the year at 53.3% — ahead of both the South East (52.6%) and close to England (54.9%). See `outputs/west_sussex_vs_england.png`.

**On the full-year figure, West Sussex still sits mid-to-lower table.** Because the annual rate blends the weak early quarters in, West Sussex ranks 14th of 19 South East local authorities at 48.4%, behind neighbours like Hampshire (53.6%) and Surrey (58.7%). See `outputs/local_authority_quit_rates.png`. The annual figure and the Q4 figure tell different stories — worth flagging in any report that only shows one number.

**West Sussex spends less per successful quitter.** £427 per quitter, against £705 for England and £737 for the South East. That's a genuinely positive finding, though a single year of data can't say whether it reflects efficient delivery or a smaller/lower-cost service mix — that would need service-level detail this dataset doesn't include.

**Routine and manual workers, and the long-term unemployed, quit at the lowest rate and lag England the most.** Both groups sit 11.7 percentage points below the England rate for the same group, and both are large groups by volume (791 and 393 quit attempts respectively) — not small samples that could be noise. Managerial and professional occupations are close to the England rate, so the gap is concentrated in exactly the groups a public health directorate would prioritise for reducing health inequalities. See `outputs/socioeconomic_quit_rate_gap.png`.

**One-to-one in-person support is West Sussex's largest support channel by volume, and its weakest.** 1,420 of West Sussex's quit attempts went through one-to-one in-person support, more than any other channel, and its quit rate trails England by 16.1 percentage points for that channel specifically. Telephone and digital mobile-app support both outperform England, though on much lower volumes (634 and 974 attempts). See `outputs/intervention_type_quit_rate_gap.png`. This doesn't prove the channel itself is the problem — it could reflect who is referred into it — but it is the single biggest lever visible in this data, since it's both the largest group and the largest shortfall.

## Recommendation (as if briefing the service)

The improvement through the year is real and worth understanding — what changed between Q2 and Q3 is the more useful question than the annual average on its own. If Q3/Q4 practices can be sustained and the current spend efficiency holds, West Sussex is on a good trajectory going into 2026/27. Worth checking whether the early-year dip was a data submission issue, a service change, or seasonal.

The socio-economic gap points to where outcomes are weakest: routine and manual occupations, and people who haven't worked in over a year. Both are large groups, not statistical noise, and both sit well below the England rate for the same group. Worth asking whether these groups are being referred into the support channel that's underperforming — one-to-one in-person is both West Sussex's highest-volume channel and its weakest, while telephone and digital support both beat the England average on the same measure. That's a testable question, not just an observation: does shifting more of the largest referral group toward the better-performing channels close some of the socio-economic gap.

## Power BI dashboard

`dashboard/WestSussex_stopsmoking_dashboard.pbix` — a one-page dashboard built on the cleaned CSV outputs from the Python analysis, not the raw NHS file, so it reflects the same suppressed-value fix and category filtering described above.

Contents:
- KPI cards: referrals, quit attempts, successful quitters, annual quit rate, cost per quitter
- Quarterly trend line chart: West Sussex vs South East vs England
- Local authority ranking bar chart, sorted by quit rate
- Socio-economic group gap chart, coloured by whether West Sussex sits above or below England
- Support type gap chart, same treatment

Requires [Power BI Desktop](https://www.microsoft.com/en-us/power-platform/products/power-bi/downloads) (free) to open.

## Files
