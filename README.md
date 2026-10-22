# Site Lembar Penagihan Harian

An automated Accounts Receivable (AR / *Piutang*) ETL pipeline and Google Sheets Add-on for managing and updating daily billing sheets (*Lembar Penagihan Harian*). This project automates master data retrieval, AR and Giro data cleaning, balance reconciliation, date standardization, and direct injection into Google Spreadsheets for finance and billing teams.

## Table of Contents

* [Features](#features)
* [Tech Stack](#tech-stack)
* [Project Structure](#project-structure)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Usage](#usage)
* [Environment Variables](#environment-variables)
* [Contributing](#contributing)
* [License](#license)

## Features

* **Automated AR ETL Pipeline**: Sequential 9-step Python data pipeline (`Dapur/`) covering master data downloading, data cleansing, merging, and formatting.
* **Giro & Receivable Cleansing**: Dedicated modules to clean standard receivables (`1_CleanerAcc.py`), process Giro records (`2_CleanerAccGiro.py`), remove zero-balance accounts (`4_CleanZeroAR.py`), and compare/reconcile Giro statuses (`5_CompareGiro.py`).
* **Date Normalization**: Automatically adjusts and standardizes date formats across financial exports (`6_AdjDateFormat.py`).
* **Direct Google Sheets Injection**: Pushes processed Accounts Receivable datasets directly to target Google Spreadsheets (`7_HelperSSData.py`, `8_ARInjectionToSS.py`).
* **Google Sheets Add-on & Smartfill**: Built-in Google Apps Script integration (`Code.gs`, `smartfill.gs`, `Index.html`) providing custom UI tools and automated smart-fill capabilities inside Google Sheets.

## Tech Stack

* **Python 3.x** — Core ETL pipeline, data manipulation, and automation scripts
* **Google Apps Script (`.gs`) & HTML5** — Google Sheets Add-on backend logic, custom UI (`Index.html`), and `smartfill.gs` automation
* **Google Sheets & Drive API** — Cloud spreadsheet synchronization via `credentials.json`

## Project Structure

```text
Site-Lembar-Penagihan-Harian-main/
├── Addon/
│   ├── Code.gs                     # Core Google Apps Script backend logic
│   ├── Index.html                  # HTML user interface for the Google Sheets Add-on
│   └── smartfill.gs                # Automated smart-fill functions for billing sheets
├── Dapur/
│   ├── __init__.py                 # Package initializer (Author: me@ihfandica.com)
│   ├── 0_DownloaderMasterData.py   # Step 0: Downloads master customer/billing data
│   ├── 1_CleanerAcc.py             # Step 1: Cleans standard Accounts Receivable data
│   ├── 2_CleanerAccGiro.py         # Step 2: Cleans Giro receivable data
│   ├── 3_MergedMaster2Main.py      # Step 3: Merges master data with main AR dataset
│   ├── 4_CleanZeroAR.py            # Step 4: Filters out zero-balance AR records
│   ├── 5_CompareGiro.py            # Step 5: Compares and reconciles Giro records
│   ├── 6_AdjDateFormat.py          # Step 6: Adjusts and standardizes date columns
│   ├── 7_HelperSSData.py           # Step 7: Helper utilities for Google Spreadsheet data
│   ├── 8_ARInjectionToSS.py        # Step 8: Injects final AR data into Google Sheets
│   ├── credentials.json            # Google Cloud Service Account / OAuth credentials
│   └── piutang.conf                # Configuration file for AR pipeline paths and IDs
├── Ekspor Data.png                 # Visual reference guide for source data export
├── Perbarui Data AR.py             # Main entry-point script to execute the AR update
└── README.md                       # Project documentation
```

## Prerequisites

* **Python 3.8+** installed on your system
* **Google Cloud Platform (GCP) Project** with Google Sheets API and Google Drive API enabled
* **Service Account / OAuth Credentials** saved as `Dapur/credentials.json`
* Access to the target **Google Spreadsheet** shared with the service account email

## Installation

1. Clone the repository:
   ```bash
   git clone [https://github.com/your-username/Site-Lembar-Penagihan-Harian.git](https://github.com/your-username/Site-Lembar-Penagihan-Harian.git)
   cd Site-Lembar-Penagihan-Harian
   ```

2. Create and activate a virtual environment (recommended):
   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # macOS / Linux
   source venv/bin/activate
   ```

3. Install the required Python dependencies:
   ```bash
   pip install pandas gspread oauth2client google-api-python-client configparser
   ```

4. Configure your credentials and settings:
   * Place your valid Google API credentials JSON file at `Dapur/credentials.json`.
   * Update `Dapur/piutang.conf` with your local file paths, spreadsheet IDs, and worksheet names.

5. Set up the Google Sheets Add-on:
   * Open your target Google Spreadsheet and navigate to **Extensions > Apps Script**.
   * Copy the contents of `Addon/Code.gs`, `Addon/smartfill.gs`, and `Addon/Index.html` into the respective files in the Apps Script editor.
   * Save and refresh your Google Sheet to load the custom menu and sidebar.

## Usage

1. Export the latest raw Accounts Receivable and Giro data from your accounting system (refer to `Ekspor Data.png` for the expected export format).
2. Run the main update script to execute the full `Dapur/` processing pipeline and update the spreadsheet:
   ```bash
   python "Perbarui Data AR.py"
   ```
3. Alternatively, run individual pipeline stages inside `Dapur/` for debugging or step-by-step execution:
   ```bash
   python Dapur/0_DownloaderMasterData.py
   python Dapur/1_CleanerAcc.py
   python Dapur/2_CleanerAccGiro.py
   python Dapur/3_MergedMaster2Main.py
   python Dapur/4_CleanZeroAR.py
   python Dapur/5_CompareGiro.py
   python Dapur/6_AdjDateFormat.py
   python Dapur/8_ARInjectionToSS.py
   ```

## Environment Variables

Configuration is managed primarily through `Dapur/piutang.conf` and `Dapur/credentials.json`.

| Variable / File | Description | Default |
| --- | --- | --- |
| `Dapur/credentials.json` | Google Cloud Service Account key file for Sheets API authentication | Required |
| `Dapur/piutang.conf` | Configuration file specifying source data paths, master data settings, and target Google Sheet IDs | Required |
| `SPREADSHEET_ID` | Target Google Spreadsheet ID configured inside `piutang.conf` | `YOUR_SPREADSHEET_ID` |
| `RAW_DATA_PATH` | Directory path for exported AR and Giro source files | `./data` |

## Contributing

Contributions are welcome. Please open an issue or submit a pull request for any bug fixes, pipeline optimizations, or Google Sheets Add-on enhancements.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📜 Lisensi

Proyek ini dikembangkan untuk keperluan internal perusahaan. Silakan sesuaikan dengan kebutuhan organisasi Anda.

---

*Dikembangkan oleh [ACC-TAX-REIGHTEEN](https://github.com/ACC-TAX-REIGHTEEN)*
