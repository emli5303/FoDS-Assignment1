# FoDS Assignment 1 — UN General Debate Corpus (UNGDC) Project

This repo analyzes the UN General Debate Corpus: speeches given by country representatives at the UN General
Assembly, together with speaker and country metadata.

## 1. Setup

1. Install Python dependencies:

   ```bash
   pip install -r requirements.txt
   ```

2. Download the spaCy language model (used in `preprocessing_advanced.ipynb` for grammar-based hedge/commitment
   detection):

   ```bash
   python -m spacy download en_core_web_sm
   ```

3. NLTK data (`punkt`, `punkt_tab`, `stopwords`) is downloaded automatically the first time you run a notebook that
   needs it (`nltk.download(...)` calls are already in the notebook cells) — just make sure you have an internet
   connection the first time you run them.

## 2. Downloading the dataset

The raw speech corpus (`datasets/TXT/`) is **not** committed to git (see `.gitignore`) because it's thousands of
files — each teammate needs to download it locally.

- **Source:** UN General Debate Corpus, Harvard Dataverse
- **Link:** https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/0TJX8Y

**Steps:**

1. Open the link above and download the corpus archive.
2. Extract it so that the speeches end up at:

   ```
   datasets/TXT/Session NN - YYYY/ISO_NN_YYYY.txt
   ```

   e.g. `datasets/TXT/Session 75 - 2020/USA_75_2020.txt`

3. Make sure the following metadata files are also present under `datasets/` (already committed to this repo, so
   you likely have them already if you cloned it):
   - `datasets/Speakers_by_session.xlsx`
   - `datasets/country_codes_V202301.csv`
   - `datasets/UNSD - Methodology.csv`

After this, `datasets/TXT/` should contain 80 session folders (`Session 01 - 1946` through `Session 80 - 2025`) and
about 11,141 `.txt` speech files in total.

## 3. Manually cleaning up corrupting / junk files

Dataset archives downloaded/extracted on a Mac (or that were originally zipped on a Mac) commonly come with extra
junk files mixed into the folders. These are **not** part of the actual corpus and should be deleted before running
the notebooks, otherwise they can get picked up as if they were real speech files. Look out for:

- `.DS_Store` files — macOS folder-view metadata
- `__MACOSX/` folders — macOS zip metadata folder
- `._*` files — macOS "AppleDouble" resource-fork files, e.g. `._USA_75_2020.txt`

We have also personally run into one specific corrupted file in this corpus that is worth checking for by name:

- `datasets/TXT/Session 47 - 1992/.DS_Store-to-UTF-8.txt`

  This is **not** a real speech — it's a renamed/mangled macOS junk file that doesn't follow the normal
  `ISO_SESSION_YEAR.txt` naming pattern. Delete it if you find it after downloading/extracting the corpus.

**How to find and remove these junk files:**

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

To just **list** suspicious files first (without deleting anything), so you can eyeball them before removing:

```bash
find datasets/TXT -type f -name "*.txt" | grep -vE '/[A-Za-z]{2,4}_[0-9]{2}_[0-9]{4}\.txt$'
```

Any file this last command prints does not match the expected `CODE_SESSION_YEAR.txt` naming pattern and is worth
checking by hand.

## 4. Project / notebook layout

- **`notebooks/preprocessing_simple.ipynb`**
  Loads the raw corpus, merges in speaker/country metadata, and produces lowercased/tokenized/stopword-removed
  text. Saves `datasets/speeches_simple_clean.parquet`.

- **`notebooks/preprocessing_advanced.ipynb`**
  Loads `speeches_simple_clean.parquet`, filters to `Year >= 2015`, and scores each speech on how much it mentions
  the energy transition and how hedged vs. committal that language is (three independent detection methods,
  cross-checked against each other). Saves `datasets/speeches_advanced_clean.parquet`.

- **`notebooks/data_cleaning_merging.ipynb`, `eda.ipynb`, `predictive_modelling.ipynb`,
  `text_preprocessing_features.ipynb`**
  Placeholders — not yet built out.
