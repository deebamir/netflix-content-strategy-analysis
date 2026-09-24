# Netflix Content Strategy Analysis

## Why I built this
I wanted a project that actually used skills close to my background — reconciliation, spotting discrepancies, working with messy real-world data — rather than just following a tutorial.
So I picked the Netflix Titles dataset and set out to answer a few questions I was genuinely curious about: has Netflix shifted from movies to TV shows, where does most of their content come from, and how long does content usually sit before it lands on Netflix?

## The dataset
Netflix Titles dataset from Kaggle — around 8,800 rows, with fields like type, country, release year, date added, rating, and duration.

## What I actually did
I started by backing up the raw data before touching anything (learned that lesson before, not the hard way this time). From there:

- Removed duplicate rows
- A lot of rows were missing `director` or `country` — instead of deleting them, I filled them with "Unknown" so I didn't lose real data just because one field was empty
- Some rows listed multiple countries per title, so I pulled out just the first one as a "primary country" to make country level analysis possible
- `date_added` was stored as text, so I converted it to an actual date
- `duration` meant two completely different things depending on whether it was a movie ("90 min") or a show ("2 Seasons") — I split it into two separate columns so I could actually compare like with like
- I calculated a "licensing lag" — basically, how many years passed between a title's original release and when it showed up on Netflix
- Then I built four pivot tables and turned each into a chart: content mix over time, top countries, rating breakdown, and licensing lag by country

## Things that went wrong (and how I fixed them)
Honestly, more of this project was debugging than I expected, which I think is a more realistic picture of what data cleaning actually looks like:

1. **Excel Online doesn't have "Go To Special → Blanks."**
I only found this out after trying it and hitting a dead end.
Fixed it with a formula instead: `=IF(F2="","Unknown",F2)`.

2. **Adding new columns shifted everything and broke my formulas.**
Every time I inserted a helper column, anything referencing cells to the right of it pointed at the wrong place.
I had to go back and re-check every formula's references after I was done adding columns, not while I was adding them.

3. **I tried to "fix" a date column that was already fixed.**
I wrote `=DATEVALUE(H2)` on a column that had already been converted to a real date, which broke it, because DATEVALUE only works on text.
Once I realized the column was already a proper date, I just referenced it directly instead.

4. **My licensing lag numbers showed up as dates.**
Turns out Excel treats small numbers as valid dates by default, so "1" was displaying as "01/01/1900."
Just had to manually change the cell format to Number.

5. **Duration needed splitting, not just cleaning.**
"90 min" and "2 Seasons" can't be compared directly, so I split them into `duration minutes` and `seasons` using formulas that check which unit is present first.

6. **Pivot Charts don't let you filter what you chart.**
I spent way too long confused about why my "top 10 countries" chart kept showing every country in the dataset.
Turns out PivotCharts are locked to the whole pivot table, no matter what you select. The fix was copying the top 10 as plain values into a separate area, then charting that instead.

7. **A few rows had garbage in the rating column.**
Some rows showed things like "66 min" as a rating, which obviously isn't a real rating — looks like a shifted or corrupted row somewhere upstream.
I excluded those from the chart and just noted it here rather than pretending they weren't there.

8. **Two "countries" that don't exist anymore skewed my licensing lag chart.**
West Germany and Soviet Union showed absurdly high average lags (40 and 38 years) — almost certainly one weirdly-dated title each, not a real pattern.
Excluded them and flagged it as a data quality issue rather than a finding.

## What I found

**Movies are declining, TV shows are rising.** 
Movie additions peaked around  2017-2018 at roughly 767 a year, then dropped to 517 by 2020.
TV shows kept climbing the whole time and nearly caught up (517 vs. 436 in 2020).
Feels like a deliberate shift in strategy, not just noise.

**The US dominates, but the catalog is genuinely global.** 
The US has 3,211 titles — about 3x India's 1,008, the next highest. 
Still, the UK, Canada, Japan, South Korea, Spain, and Mexico all crack the top 10, so it's not just an American platform with some extras thrown in.

**The catalog skews mature.** 
TV-MA and TV-14 together make up over 60% of everything.
Content rated for kids (G, TV-G, TV-Y, TV-Y7, PG) is a small slice by comparison.

**Licensing lag doesn't track with how much content a country produces.** 
Hong Kong has the longest average lag (16.7 years), followed by Poland and Egypt. 
India, despite being the second-biggest source of content by volume, has a comparatively short lag (6.8 years) — makes me think high-output countries might have more direct or faster-moving licensing/production deals, while smaller markets' catalogs trickle in later, probably older, previously-licensed content rather than new releases.

## What's in this repo
- `netflix-content-strategy-analysis.xlsx` — the full workbook, cleaned data, pivot tables, and charts
- `content-mix-trend.png`
- `top-countries.png`
- `ratings-distribution.png`
- `licensing-lag.png`
