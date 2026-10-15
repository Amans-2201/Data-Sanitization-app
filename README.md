# Data Sanitization Workbench

A static, browser-based CSV data-quality workbench. Upload a CSV, review local checks, optionally ask Gemini to analyze a sample and suggest corrections, then select the proposed changes to apply before downloading a cleaned CSV.

## Features

- Parses CSV headers, quoted fields, escaped quotes, and line breaks inside quoted values.
- Shows a preview of the uploaded data.
- Runs local checks for blank cells, common email/phone/date/numeric formats, leading or trailing whitespace, suspicious text, and repeated identifiers or exact rows.
- Optionally sends a bounded sample of the CSV to Gemini 3 Flash Preview for schema-aware findings and row-level correction proposals.
- Lets you review and deselect individual local or Gemini proposals before applying them.
- Exports the selected corrections as a CSV while preserving the original columns.
- Includes deliberately messy customer and product CSV fixtures for testing.

## Run the app

No build step or package installation is required. Serve this folder with any static web server, then open the local URL in a browser. For example, if Python is installed:

```sh
python -m http.server 8000
```

Open `http://localhost:8000/`. The `index.html` entry point is also ready for static hosting platforms such as Netlify; publish the repository root as the site directory.

The page loads Tailwind CSS and fonts from external CDNs. Internet access is needed for those assets and for Gemini analysis.

## Use Gemini analysis

1. Create a Gemini API key in Google AI Studio.
2. Open **Gemini API key** in the app header, enter the key, and select **Save key**.
3. Upload a CSV and run **Data Quality Analysis**.
4. Review the local checks and Gemini findings. Select **Proceed to Review & Confirm Fixes** to inspect proposed row-level changes.
5. Leave checked only the changes you want, then confirm to apply them. Download the resulting CSV from the export step.

The app keeps the API key in page memory only; it is not saved to local storage. The key is sent directly from the browser to Google's Gemini API. For Gemini analysis, the app sends the CSV headers and a bounded sample (up to 100 rows, 80 columns, and approximately 18 KB of sample data). Do not analyze sensitive or confidential data unless you are authorized to share it with Google.

Local checks work without an API key. Gemini corrections are suggestions, not guaranteed truths: inspect each proposed change before applying it. The app validates suggested cell changes against the uploaded values and does not fill missing data with invented placeholders.

## Test datasets

- `sample_dirty_customers.csv` contains customer records with whitespace, missing values, inconsistent formats, and duplicates.
- `sample_dirty_products.csv` contains 50 product records with missing and malformed values, whitespace, suspicious text, and a repeated SKU.

Both are intentionally dirty test fixtures, not production data. The `.gitignore` excludes CSV files by default but includes these two named sample files; generated exports and other CSVs remain ignored.

## Current limitations

- This is a static client-side workbench; it has no server-side storage or database integration.
- The **Commit to Render Postgres DB** button is a placeholder and does not connect to or write to PostgreSQL.
- Local format checks are heuristic and only validate common types inferred from column names. Review the data and all AI-proposed changes before relying on the output.
