# NASA EVA Dataset Cleansing & Transformation Case Study

## 🚀 Project Overview
Raw historical datasets are rarely analysis-ready. This portfolio project documents a complete "Before & After" data cleaning pipeline built on the historical **NASA Extra-vehicular Activity (EVA)** dataset. 

Using **Google Sheets**, I tackled classic real-world data hygiene issues:
* **The Time-Formatting Trap:** Converting text-formatted elapsed times (`2:07`) into exact numeric minutes (`127`) to make data PivotTable-ready.
* **Historical Imputation & Primary Keys:** Standardizing missing mission dates and assigning explicit identifiers (`N/A`) to unnumbered emergency spacewalks.
* **Encoding Hygiene:** Stripping Mojibake artifacts (`Â` and square boxes) introduced by legacy file archives.

---

## 1. The "Before" State: Identifying Data Hygiene Challenges
When I first imported the dataset, it presented several critical roadblocks common to legacy logs:
* **The Time-Formatting Trap:** The `Duration` column stored values as text strings formatted like `2:07` (hours and minutes). Spreadsheets natively interpret these as times of day (e.g., 2:07 AM) rather than elapsed time, which completely breaks aggregations, averages, and PivotTables.
* **Missing Historical Data & Null Gaps:** Critical events—such as Gemini VIII mission dates or un-timed emergency spacewalks (e.g., Skylab 2)—left blank cells and unnumbered primary keys (`EVA #`), causing structural errors in SQL imports and sorting algorithms.
* **Encoding Artifacts (Mojibake):** Mismatched character encodings between legacy archives and modern imports introduced corrupted symbols, such as stray `Â` characters and unrendered square boxes in rows 375–376.

---

## 2. Step-by-Step Problem Solving & Troubleshooting

### A. Overcoming the Duration Text-Parsing Trap
* **The Challenge:** Initial attempts to extract time values using built-in spreadsheet math (`HOUR()` and `MINUTE()`) resulted in zeros because the imported cells were treated as text strings. Furthermore, dragging formulas too far down introduced `#VALUE!` errors when hitting empty rows.
* **The Solution:** I implemented a granular text-extraction formula combining string positioning and value conversion:
  `=VALUE(LEFT(F2, FIND(":", F2) - 1)) * 60 + VALUE(RIGHT(F2, LEN(F2) - FIND(":", F2)))`
* **Handling Formatting:** Because Google Sheets automatically applied a *Time* format to the new column (causing numbers like `127` to display as `0:00`), I overrode the format to a standard **Number**, stripped unnecessary decimals, and hardcoded the final values (`Paste special > Values only`) to secure a PivotTable-ready metric representing total minutes.

### B. Standardizing Missing Data & Primary Keys
* **The Challenge:** Leaving primary key cells (`EVA #`) or descriptive fields entirely blank introduces ambiguity.
* **The Solution:** For unnumbered emergency internal spacewalks (such as the Skylab 2 hatch repair), I assigned an explicit text placeholder (`N/A`) to maintain structural integrity. For mission descriptions, standardized flags like `Unknown` were utilized to ensure transparency.

### C. Cleaning Encoding Corruption (Mojibake)
* **The Challenge:** Rows 375 and 376 contained character corruption artifacts.
* **The Solution:** I isolated and cleaned these artifacts using targeted string replacements and manual spot-checking to restore clean punctuation.

---

## 3. The "After" State: Portfolio-Ready Results
After systematic intervention, the dataset achieved full production-readiness:
* **Headers:** Uniformly frozen, bolded, and scrubbed of trailing residue.
* **Metrics:** Column F was successfully transformed into a pristine, numeric `Duration (Minutes)` column.
* **Integrity:** Zero blank primary keys or unhandled formatting traps remain.

---

## Visual Comparison

### Before: Raw, Uncleaned Dataset
*Notice the text-formatted duration strings (`2:07`), missing dates, and blank primary keys.*
![Raw Dataset](Raw%20Data%20ScreenShot%20Before%20Cleaning.jpg)

### After: Polished, Production-Ready Dataset
*Notice the clean integer minutes (`127`, `50`), standardized placeholders, and robust structure.*
![Cleaned Dataset](Cleaned%20NASA%20The%20Astronaut%20EVA%20Dataset.jpg)

```
