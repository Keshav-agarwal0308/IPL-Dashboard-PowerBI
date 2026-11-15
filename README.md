# IPL Analysis Dashboard (2008-2022)

An interactive Power BI dashboard that summarizes 15 seasons of Indian Premier League cricket: season winners, top performers, boundary counts, team success, venue trends, and toss decisions, with a per-batter drill-down.

## What the dashboard shows

The report is a single 16:9 page with a **season slicer** at the top and a **batter slicer** in the player section. Everything responds to the selected season(s).

**Season summary**
- **Title winner:** the winning team of the last match in the selected season(s), which is the final.
- **Orange Cap:** the batter with the most runs, with the run total.
- **Purple Cap:** the bowler with the most wickets, with the wicket total.
- **Sixes and Fours:** total boundaries hit in the selected season(s).

**Player stats** (choose a batter with the slicer)
- Total runs, fours, sixes, and strike rate for that batter.

**Match insights**
- **Won by venue:** the top 5 venues by matches, split into wins by runs vs. wins by wickets.
- **Teams that won the most:** the top 5 teams by number of wins.
- **Toss decision:** share of tosses won where the team chose to bat vs. field.

## Data

Two CSV files covering IPL 2008-2022:

| Table | Grain | Key columns |
|---|---|---|
| `ipl_ball_by_ball_2008_2022` | One row per delivery | `id`, `innings`, `overs`, `ball_number`, `batter`, `bowler`, `batsman_run`, `total_run`, `iswicket_delivery`, `dismisal_kind`, `non_boundary`, `batting_team` |
| `ipl_matches_2008_2022` | One row per match | `id`, `match_date`, `season`, `venue`, `team1`, `team2`, `toss_winner`, `toss_decision`, `winning_team`, `won_by`, `margin`, `player_of_match` |

A generated `datedemo` calendar table (from the earliest to the latest `match_date`) provides the season/year slicer.

## Data model

- `ipl_ball_by_ball_2008_2022` (many) to `ipl_matches_2008_2022` (one), joined on match `id`, single-direction filtering.
- A `MyMeasures` table holds the DAX measures.

## Data preparation (Power Query)

- Loaded both CSVs and set explicit column types (integers, text, dates).
- Standardized the franchise name "Rising Pune Supergiant" to "Rising Pune Supergiants" in the team columns of the matches table, so the team is counted once in the charts.

## DAX measures

| Measure | Logic |
|---|---|
| `TitleWinner` | Finds the latest match date in the current selection and returns the winning team of that match |
| `orangecap` | Sum of `batsman_run`, shown with a "Runs" label; the card is filtered to the top 1 batter |
| `purplecap` | Sum of `iswicket_delivery`, shown with a "Wickets" label; the card is filtered to the top 1 bowler |
| `strikerate` | `DIVIDE(SUM(batsman_run), COUNT(ball_number)) * 100` |

**Filtering choices**
- Purple Cap only counts dismissals credited to the bowler: bowled, caught, caught and bowled, stumped, LBW, and hit wicket. Run outs and other non-bowler dismissals are excluded.
- Fours and sixes only count deliveries where `non_boundary = 0`, so runs from overthrows are not counted as boundaries.

## How to open it

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows).
2. Open `IPL_Analysis_Dashboard.pbix`.
3. The CSV sources are configured with local file paths. If Power BI cannot find them, go to **Transform data > Data source settings > Change Source** and point both queries to your copies of the CSV files, then click **Refresh**.

## Notes and possible improvements

- Strike rate currently divides runs by every delivery row, including wides, so it is a slight simplification of the standard balls-faced definition. Excluding wides (`extra_type`) would make it exact.
- Possible extensions: bowler-level drill-down (economy rate), head-to-head team comparison, player-of-the-match leaders, and a phase-of-play view (powerplay vs. death overs).

## Tech

Power BI Desktop, Power Query (M), DAX
