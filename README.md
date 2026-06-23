CSV File Checker
A pre-load validation tool for CSV files headed into BigQuery, MySQL, or Google Sheets. It catches the issues that most often break an upstream load or quietly corrupt data — encoding problems, BOM contamination, ragged rows, duplicate or whitespace-tainted column names, blank rows, and missing values — then produces a cleaned copy and a summary report.

Built as part of a QA / data-quality workflow: the same export gets validated the same way every time, before it ever reaches a database.

Why this exists
A CSV that looks fine in Excel can still break a load or silently corrupt a table:

A UTF-8 BOM puts a hidden \ufeff prefix on the first column name, so id becomes \ufeffid in BigQuery and every reference to it fails.
Wrong encoding (Latin-1 / Windows-1252 saved as if it were UTF-8) turns accented characters into mojibake once loaded.
Whitespace in column names produces phantom columns that don't match your schema.
Blank and duplicate rows inflate counts and skew aggregates.
This notebook runs the same set of checks every time, reports what it finds, and writes a cleaned copy you can load with confidence.

What it checks
#	Check	What it catches
1	BOM detection	Hidden \ufeff prefix from Excel-saved UTF-8
2	Encoding detection	Latin-1 / Windows-1252 vs. UTF-8
3	Column-name issues	BOM, leading/trailing spaces, empty names
4	Blank rows	Fully empty rows
5	Duplicate rows	Exact full-row duplicates
6	Null summary	Per-column null counts and percentages
7	Shape & data types	Row/column counts and inferred dtypes
The clean step then strips any BOM from column names, trims whitespace, removes blank and duplicate rows, re-encodes string columns to UTF-8, and writes the result out.

Quickstart
pip install -r requirements.txt
jupyter notebook csv_file_checker.ipynb
Run the cells top to bottom. Either use the upload widget (needs ipywidgets) or set FILE_PATH to your CSV. A sample file is included at sample_data/test_sample.csv so the notebook runs end-to-end out of the box.

Output
<input>_clean.csv — UTF-8 (no BOM), blank and duplicate rows removed, column names trimmed.
A printed summary report consolidating every check result.
Project structure
csv-file-checker/
├── csv_file_checker.ipynb     # the validator
├── sample_data/
│   └── test_sample.csv        # synthetic sample (runs out of the box)
├── requirements.txt
├── LICENSE
└── README.md
Requirements
Python 3.9+
pandas, chardet, ipywidgets (optional, for the upload widget), Jupyter
See requirements.txt.
