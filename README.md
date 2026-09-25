# FoDS Assignment 1 — Do countries practice what they preach?

This repo combines the UN General Debate Corpus (UNGDC) with World Bank and Our World in Data (OWID) energy data to
compare what countries *say* about the energy transition at the UN with what they *do*.

- **RQ1 (exploratory):** Among the world's major fossil-fuel economies, how does the frequency of energy-transition
  mentions in their UN General Debate speeches correlate with the fossil-fuel share of their primary energy
  consumption after the 2015 Paris Agreement?
- **RQ2 (predictive):** Can how a country talks about the energy transition at the UN — how often, and how
  cautiously — predict the change in its fossil-fuel production over the following year, and is that relationship
  different for the major fossil-fuel economies?

## 1. Setup

Tested with Python 3.10.4.

1. Install the Python dependencies:

   ```bash
   pip install -r requirements.txt
   ```

2. Download the spaCy language model (used in `02_preprocessing_advanced.ipynb` for grammar-based hedge/commitment
   detection; the notebook also downloads it automatically if it is missing):

   ```bash
   python -m spacy download en_core_web_sm
   ```

3. NLTK data (`punkt`, `punkt_tab`) is downloaded automatically by `02_preprocessing_advanced.ipynb` the first time
   it runs, so you need an internet connection then.

Open the notebooks in any Jupyter front end (JupyterLab, Jupyter Notebook or VS Code); `ipykernel` is included in the
requirements.

## 2. Data

### 2.1 UN General Debate Corpus (download it yourself)

The raw speech corpus (`datasets/TXT/`) is **not** committed to git (see `.gitignore`), because it is about 11,000
files.

- **Source:** UN General Debate Corpus, Harvard Dataverse:
  https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/0TJX8Y

1. Download the corpus archive from the link above.
2. Extract it so that the speeches end up at `datasets/TXT/Session NN - YYYY/ISO_NN_YYYY.txt`, e.g.
   `datasets/TXT/Session 75 - 2020/USA_75_2020.txt`.
3. Afterwards `datasets/TXT/` should contain 80 session folders (`Session 01 - 1946` to `Session 80 - 2025`) and
   11,141 `.txt` speech files.

### 2.2 Removing junk files from the corpus

Archives made on a Mac often contain extra files that are not speeches: `.DS_Store` files, `__MACOSX/` folders and
`._*` "AppleDouble" files. We also found one mangled file, `datasets/TXT/Session 47 - 1992/.DS_Store-to-UTF-8.txt`.
Delete these before running notebook 01, otherwise they are read as if they were speeches.

Windows (PowerShell), from the project root:

```powershell
Get-ChildItem -Path datasets\TXT -Recurse -Force `
    -Include ".DS_Store", "__MACOSX", "._*", ".DS_Store-to-UTF-8.txt" |
    Remove-Item -Recurse -Force
```

Mac/Linux/Git Bash, from the project root:

```bash
find datasets/TXT \( -iname ".DS_Store" -o -iname "__MACOSX" \
    -o -iname "._*" -o -iname ".DS_Store-to-UTF-8.txt" \) \
    -exec rm -rf {} +
```

To list any remaining file that does not follow the `CODE_SESSION_YEAR.txt` naming pattern:

```bash
find datasets/TXT -type f -name "*.txt" | grep -vE '/[A-Za-z]{2,4}_[0-9]{2}_[0-9]{4}\.txt$'
```

### 2.3 Metadata (included in the repo)

| File | Content | Source |
|---|---|---|
| `datasets/Speakers_by_session.xlsx` | Speaker name and post per speech | Distributed with the UNGDC (link above) |
| `datasets/UNSD — Methodology.csv` | Country names, regions and sub-regions (M49 standard) | UN Statistics Division, https://unstats.un.org/unsd/methodology/m49/overview/ |

`datasets/country_codes_V202301.csv` is not used by the current notebooks.

### 2.4 External datasets (included in the repo, `datasets/Raw Data/`)

| Folder | Indicator | Source |
|---|---|---|
| `API_NY.GDP.PETR.RT.ZS_…` | Oil rents (% of GDP) | World Bank, World Development Indicators, https://data.worldbank.org/indicator/NY.GDP.PETR.RT.ZS |
| `API_NY.GDP.COAL.RT.ZS_…` | Coal rents (% of GDP) | World Bank, World Development Indicators, https://data.worldbank.org/indicator/NY.GDP.COAL.RT.ZS |
| `API_NY.GDP.NGAS.RT.ZS_…` | Natural gas rents (% of GDP) | World Bank, World Development Indicators, https://data.worldbank.org/indicator/NY.GDP.NGAS.RT.ZS |
| `energy-mix/` | Share of primary energy from fossil fuels (%) | Our World in Data, based on the Energy Institute *Statistical Review of World Energy* (2026), https://ourworldindata.org/grapher/energy-mix?source=fossil_fuels&metric=share |
| `fossil-fuel-production/` | Coal, oil and gas production (TWh) | Our World in Data, based on the Energy Institute *Statistical Review of World Energy* (2026), https://ourworldindata.org/grapher/fossil-fuel-production |

The World Bank files are the CSV downloads from the indicator pages (data last updated 13 July 2026). The OWID folders
are the "full data" packages from the chart pages, each with its own `readme.md` and metadata; the energy-mix package
was downloaded on 22 September 2026.

## 3. Notebooks

Run the notebooks in order. Each one reads the output of the earlier ones, so a clean run is:
**01 → 02 → 03 → 04 → 05 → 06**. (Notebook 03 does not depend on 01–02, but has to run before 04.)

| Notebook | What it does | Writes |
|---|---|---|
| `01_preprocessing_simple.ipynb` | Loads all 11,141 speeches, merges speaker and M49 region metadata, rejoins words split across line breaks, adds word counts | `datasets/speeches_simple_clean.parquet` |
| `02_preprocessing_advanced.ipynb` | Keeps speeches from 2015 on (Paris Agreement), counts energy-transition mentions and scores hedging vs. commitment language with three methods | `datasets/speeches_advanced_clean.parquet` |
| `03_external_data.ipynb` | Loads the World Bank and OWID data, removes aggregate regions and merges everything into one country-year panel | `datasets/prepared_data/fossil_rents_by_country_year.csv`, `energy_mix_fossil_share_by_country_year.csv`, `fossil_fuel_production_by_country_year.csv`, `master_panel_country_year.csv` |
| `04_eda.ipynb` | Defines the 15 major fossil-fuel economies (top quartile of both production and rents) | `datasets/prepared_data/major_fossil_economies.csv`, `figures/eda_*.png` |
| `05_eda_combined.ipynb` | RQ1: correlates transition talk with the fossil share of the energy mix (pooled, between and within countries, with robustness checks) | `datasets/prepared_data/rq1_*.csv`, `figures/rq1_*.png` |
| `06_predictive_modelling.ipynb` | RQ2: OLS inference with robust and clustered standard errors, and a model comparison (baseline, linear, Ridge, random forest) with time-ordered cross-validation | `datasets/prepared_data/rq2_*.csv`, `figures/rq2_target_distribution.png` |

Notebook 01 takes about 4 minutes (it reads every speech file) and 02 about 1.5 minutes; the others take under
30 seconds each.

`datasets/speeches_simple_clean.parquet` is stored with Git LFS (see `.gitattributes`). If you cloned the repo
without Git LFS, that file is only a pointer; install Git LFS or regenerate it by running notebook 01.

## 4. Repository layout

```
datasets/
  TXT/                  raw speeches (download yourself, git-ignored)
  Raw Data/             World Bank and OWID source files
  prepared_data/        outputs of notebooks 03–06
  speeches_*.parquet    outputs of notebooks 01–02
  Speakers_by_session.xlsx, UNSD — Methodology.csv
figures/                figures saved by notebooks 04–06
notebooks/              01–06, see above
report.tex              the report
requirements.txt
```
