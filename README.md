# Figure Skating Scoring Analysis
 
A data science project that scrapes, cleans, and analyzes competitive figure skating judging data from ISU Grand Prix events (2024/2025 season) and the 2026 Olympics, to explore how technical difficulty and execution quality relate to subjective (artistic) scoring, and to build a regression model that predicts element-level scores. This project was completed as a capstone project for Kappa Theta Pi Pre-Professional Fraternity at Northwestern University. 
 
**[View the live dashboard](https://sophiap214.github.io/FigureSkatingAnalytics/)**

## Background
As a data science major with an interest in sports analytics, I thought it would be fun to do a project on figure skating as the Winter Olympics 2026 were occurring at the time. I actually grew up figure skating, starting around 7 years old and stopping when I was around 12. Therefore, I loved watching the Winter Olympics because I not only knew how the sport was scored and evaluated, but I knew how difficult it was for these athletes to execute their routines. However, because of recent comments from the skaters themselves, I was curious to see if skating data showed any signs of bias from the judges.

## Project Overview
 
Figure skating scores combine two components:
- **Technical Elements Score (TES)** — points for jumps, spins, and step sequences, based on a Base Value plus a Grade of Execution (GOE) bonus/penalty.
- **Program Component Score (PCS)** — subjective judge ratings for skating skills, composition, and presentation.
This project asks three central questions:
1. Does technical difficulty influence artistic (PCS) scores?
2. Are some judges systematically stricter or more lenient than the panel average?
3. Are harder elements "worth it," or do PCS scores compensate for a lack of difficulty?
It also builds a linear regression model to predict GOE and element scores, then tests that model on 2026 Olympic Free Skate data.
 
## Data Sources
 
- **ISU Grand Prix Series judging protocol PDFs** (2024/2025 season) for Men's and Women's Free Skate:
  - Allen (US Classic), Skate Canada, France (Grand Prix de France), Japan (NHK Trophy), and the Grand Prix Final
- **2026 Winter Olympics** Men's and Women's Free Skate judging protocols (used as held-out test data)
- Supplementary sources explored during data collection: `results.isu.org` result tables and `isuresults.com` skater bio pages (ultimately not used, since they lacked element-level scoring detail)
Raw PDFs are parsed using `pdfplumber` (and, during exploration, `camelot-py`) to extract per-skater element scores (jumps, spins, step sequences) and per-judge Program Component Scores.
 
## Repository Structure
 
```
├── extracted_CSVs/            # Per-event and master CSV files produced by the scraping notebooks
├── jupyter_notebooks/         # All analysis notebooks (see below)
├── original_pdfs/             # Source ISU judging protocol PDFs (Grand Prix + Olympics)
├── KTP_Capstone_FINAL.html    # Rendered HTML export of the final capstone notebook/report
├── all_elements.csv           # Master technical elements dataset (all competitions combined)
├── all_pcs.csv                # Master Program Component Score dataset (all competitions combined)
├── isu_scores.csv             # Raw scraped ISU results table (exploratory, from DataCleaning.ipynb)
└── README.md
```
 
## Notebooks
 
Notebooks live in `jupyter_notebooks/` and are designed to be run in the following order:
 
| Order | Notebook | Purpose |
|---|---|---|
| 1 | `DataCleaning.ipynb` | Exploratory work testing different scraping approaches (ISU results tables, skater bio pages, `camelot-py`, `pdfplumber`) to find the most reliable way to extract element-level and PCS data from judging protocol PDFs. Also prototypes reshaping the scraped data from long to wide format. |
| 2 | `Collecting_All_Data.ipynb` | Scrapes judging protocol PDFs (from `original_pdfs/`) for every 2024/2025 Grand Prix event (Allen, Canada, France, Japan, Grand Prix Final) for both Men's and Women's Free Skate, extracting element and PCS data into individual CSV files (saved to `extracted_CSVs/`). |
| 3 | `Putting_It_All_Together.ipynb` | Combines all per-event CSV files into two master datasets (`all_elements.csv` and `all_pcs.csv`), tagging each row with gender, competition, season, and segment metadata. |
| 4 | `Data_Analysis_Viz.ipynb` | Analyzes the combined dataset to answer the three core research questions using correlation analysis, OLS regression (`statsmodels`), and visualizations (`matplotlib`/`seaborn`), broken down by gender and competition. Also profiles an individual skater (Alysa Liu) across the season. |
| 5 | `Build_a_Regression_Model.ipynb` | Scrapes 2026 Olympic Free Skate protocols as test data, engineers features (e.g. jump type, combo flag, element order, skater-level historical averages), trains `scikit-learn` `LinearRegression` models to predict GOE and element score, and evaluates predictions against actual Olympic results. |
 
`KTP_Capstone_FINAL.html` is the source for the **Figure Skating Analytics Dashboard** — a static, self-contained report of the full analysis and outputs, deployed via GitHub Pages at the link above. You can also open it directly in a browser to view it locally without running any code.
 
## Methodology
 
**Data extraction:** Judging protocol PDFs are parsed with `pdfplumber`; text is split into per-skater blocks and parsed with regular expressions to pull out element names, base values, GOE, judge-by-judge scores, and PCS components.
 
**Feature engineering:** Features used for modeling include whether an element is a jump, whether it's a combination, whether it occurs late in the program (bonus-eligible), and skater-level historical average GOE/score.
 
**Modeling:** Two `LinearRegression` models are trained — one to predict GOE, and one to predict the execution bonus added to Base Value — then combined to produce a full predicted element score. Model performance is evaluated using RMSE on a validation split and by comparing predicted vs. actual Total Element Scores on the 2026 Olympics test set.
 
**Statistical analysis:** OLS regression (`statsmodels`) is used to test the significance of Base Value and Average GOE as predictors of total PCS, and judge-level deviations from the panel average are computed to assess judging consistency.
 
## Key Findings
 
- **Execution matters more than difficulty:** Average GOE is a much stronger predictor of PCS than technical Base Value, suggesting judges reward clean execution more than raw difficulty.
- **Small "halo effect":** Skaters attempting harder elements do receive a modest PCS boost, but the effect is small compared to execution quality.
- **Gender differences:** The relationship between GOE/Base Value and PCS appears steeper for women than men, suggesting PCS may be more sensitive to execution and difficulty for women's programs.
## Tech Stack
 
- **Data collection:** `pdfplumber`, `camelot-py`, `requests`, `BeautifulSoup`
- **Data wrangling:** `pandas`, `numpy`
- **Statistics & modeling:** `scikit-learn`, `statsmodels`
- **Visualization:** `matplotlib`, `seaborn`
## Setup
 
```bash
pip install pandas numpy pdfplumber "camelot-py[cv]" requests beautifulsoup4 scikit-learn statsmodels matplotlib seaborn
```
 
Run the notebooks in `jupyter_notebooks/` in the order listed above. Note that `Collecting_All_Data.ipynb` and `Build_a_Regression_Model.ipynb` expect the source judging protocol PDFs to be present in `original_pdfs/`.
 
## Future Work
 
- Expand the dataset to additional seasons and competitions (e.g. full ISU Championship season, other disciplines like Pairs and Ice Dance)
- Incorporate non-linear models (e.g. random forest, gradient boosting) to capture more complex scoring relationships
- Formalize judge bias detection into a repeatable scoring-consistency report
 






