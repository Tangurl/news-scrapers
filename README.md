# 📰 News Capturing Orchestrator

An automated multi-portal Thai news scraping and data aggregation engine. It orchestrates parallel scrapers across **37+ national and regional news outlets**, standardizes article metadata (dates, categories, headlines, URLs), deduplicates records, and compiles them into a unified dataset with optional Google Sheets synchronization.

---

## 📁 Project Structure

The project has been streamlined so that the root folder contains only the master orchestrator, documentation, and the centralized `scrapers/` directory:

```
NewsCapturing/
├── run_all.py              # Master orchestrator, worker pool, deduplicator & sheet sync
├── README.md               # Up-to-date documentation & operational guide
└── scrapers/               # 37+ media outlet scraper packages
    ├── amarintv-scrapers/
    ├── bangkokbiznews-scrapers/
    ├── bangkokpost-scrapers/
    ├── banmuang-scrapers/
    ├── ch7-scrapers/
    ├── chiangmainews-scrapers/
    ├── dailynews-scrapers/
    ├── ejan-scrapers/
    ├── honekrasae-scrapers/
    ├── kaohoon-scrapers/
    ├── khaosod-scrapers/
    ├── khaosodenglish-scrapers/
    ├── komchadluek-scrapers/
    ├── koratdaily-scrapers/
    ├── matichon-scrapers/
    ├── mcot-scrapers/
    ├── mgronline-scrapers/
    ├── naewna-scrapers/
    ├── nationthailand-scrapers/
    ├── nationtv-scrapers/
    ├── nbt-scrapers/
    ├── nextnewsth-scrapers/
    ├── posttoday-scrapers/
    ├── pptv-scrapers/
    ├── prachachat-scrapers/
    ├── siamrath-scrapers/
    ├── springnews-scrapers/
    ├── thainews-scrapers/
    ├── thaipbs-scrapers/
    ├── thaipbsworld-scrapers/
    ├── thaipost-scrapers/
    ├── thairath-scrapers/
    ├── thansettakij-scrapers/
    ├── thestandard-scrapers/
    ├── tnews-scrapers/
    ├── tnnthailand-scrapers/
    └── workpointtoday-scrapers/
```

---

## ⚡ Prerequisites & Installation

### 1. Requirements
* **Python 3.10+**
* **Google Chrome** (latest stable version installed on the host system)

### 2. Install Dependencies
Install the required packages using pip:

```bash
pip install selenium requests gspread google-auth google-auth-oauthlib python-dotenv
```

---

## 🚀 Usage Guide

All scraping, merging, and synchronization operations are managed through `run_all.py`.

### 1. Default Run (Scrape Today's News)
Runs all 37+ outlets for today's articles with a concurrency pool of 5 workers, automatically merges all individual outputs into `master_scraped_data.csv`, and syncs to Google Sheets if configured:

```bash
python run_all.py
```

### 2. Scrape Specific Date Windows (`-d` / `--days`)
* **Scrape yesterday only:**
  ```bash
  python run_all.py -d -1
  ```
* **Scrape yesterday and the day before:**
  ```bash
  python run_all.py -d -2
  ```
* **Scrape the past 7 days (today + last 6 days):**
  ```bash
  python run_all.py -d 7
  ```

### 3. Filter Specific Media Channels (`-ch` / `--channel`)
Run only specific news portals (comma-separated):

```bash
# Single outlet
python run_all.py -ch thaipbs

# Multiple outlets
python run_all.py -ch "thaipbs,thairath,ch7,matichon"
```

### 4. Fast Merge Only (`-m` / `--merge-only`)
Combines all already-scraped CSV files from the `scrapers/` subdirectories into `master_scraped_data.csv` without launching any browser instances:

```bash
python run_all.py -m
```

### 5. Automated Daily Loop (`--time`)
Runs a scrape once immediately, then automatically sleeps and triggers every day at the designated time (Asia/Bangkok timezone):

```bash
python run_all.py -d -1 --time "09:00"
```

### 6. Concurrency & Timeout Control
* **Adjust worker pool** (default is 5 parallel browser instances):
  ```bash
  python run_all.py --workers 6
  ```
* **Verbose log streaming** (view real-time console output from child scrapers):
  ```bash
  python run_all.py -v
  ```
* **Override scraper timeout** (in seconds):
  ```bash
  python run_all.py -t 600
  ```

---

## ⚙️ Google Sheets Integration (Optional)

`run_all.py` can automatically synchronize the merged dataset to a Google Sheet.

### Configuration
1. Create a `.env` file in the project root:
   ```env
   GOOGLE_SHEET_ID="<YOUR_SPREADSHEET_ID>"
   TZ="Asia/Bangkok"
   ```
   *(Or pass `--sheet-id <ID>` directly on the CLI)*

2. **OAuth Credentials:** Place your Google OAuth 2.0 `token.json` in:
   * `scrapers/thaipbs-scrapers/token.json`, OR
   * `token.json` at the project root.

3. **Skip Google Sheet Sync:** To save data locally only without updating Google Sheets, pass `--no-sheet`:
   ```bash
   python run_all.py --no-sheet
   ```

---

## 📊 Master Dataset Schema (`master_scraped_data.csv`)

| Column | Header | Example | Description |
|:---:|:---|:---|:---|
| **A** | `date` | `2026-10-01 08:30:00` | Normalized publication datetime (YYYY-MM-DD HH:MM:SS) |
| **B** | `category` | `การเมือง`, `เศรษฐกิจ`, `กีฬา` | Standardized news category |
| **C** | `channel` | `ThaiPBS`, `Thairath`, `Khaosod` | Name of the publishing news organization |
| **D** | `article title` | `ครม. มีมติเห็นชอบ...` | Full article headline |
| **E** | `article link` | `https://...` | Canonical article URL (used for deduplication) |

---

## 📰 Covered News Outlets (37 Channels)

| Channel Directory | Media Name | Channel Directory | Media Name |
|:---|:---|:---|:---|
| `amarintv-scrapers` | Amarin TV | `nationtv-scrapers` | Nation TV |
| `bangkokbiznews-scrapers` | Bangkok Biz News | `nbt-scrapers` | NBT |
| `bangkokpost-scrapers` | Bangkok Post | `nextnewsth-scrapers` | Next News TH |
| `banmuang-scrapers` | Banmuang | `posttoday-scrapers` | Post Today |
| `ch7-scrapers` | CH7 | `pptv-scrapers` | PPTV |
| `chiangmainews-scrapers` | Chiang Mai News | `prachachat-scrapers` | Prachachat |
| `dailynews-scrapers` | Daily News | `siamrath-scrapers` | Siamrath |
| `ejan-scrapers` | EJan | `springnews-scrapers` | Spring News |
| `honekrasae-scrapers` | Hone Krasae | `thainews-scrapers` | Thai News |
| `kaohoon-scrapers` | Kaohoon | `thaipbs-scrapers` | Thai PBS |
| `khaosod-scrapers` | Khaosod | `thaipbsworld-scrapers` | Thai PBS World |
| `khaosodenglish-scrapers` | Khaosod English | `thaipost-scrapers` | ThaiPost |
| `komchadluek-scrapers` | Komchadluek | `thairath-scrapers` | Thairath |
| `koratdaily-scrapers` | Korat Daily | `thansettakij-scrapers` | Thansettakij |
| `matichon-scrapers` | Matichon | `thestandard-scrapers` | The Standard |
| `mcot-scrapers` | MCOT | `tnews-scrapers` | TNews |
| `mgronline-scrapers` | MGR Online | `tnnthailand-scrapers` | TNN Thailand |
| `naewna-scrapers` | Naewna | `workpointtoday-scrapers` | Workpoint Today |
| `nationthailand-scrapers` | Nation Thailand | | |
