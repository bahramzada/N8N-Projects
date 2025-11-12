# 🧠 N8N Projects
#### A collection of automation workflows and integrations created with **n8n**.

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/53/N8n-logo-new.svg/2560px-N8n-logo-new.svg.png" alt="Logo" width="240"/>
</p>

---

## 📖 Table of Contents
- [Overview](#overview)
- [Workflows](#workflows)
- [About Each Workflow](#about-each-workflow)
  - [Bina.az Real Estate Scraper & Database Sync](#binaaz-real-estate-scraper--database-sync)
  - [Glorri.az Job Scraper & Email Sender](#glorriaz-job-scraper--email-sender)
- [Requirements](#requirements)
- [Performance & Monitoring](#performance--monitoring)
- [Future Enhancements](#future-enhancements)
- [Notes](#notes)

---

## 🧩 Overview

This repository contains reusable **n8n workflows (JSON files)** built for automation, data collection, and integration tasks.  
Each workflow is self-contained, documented, and can be directly imported into your n8n instance.

---

## ⚙️ Workflows

| Workflow | Description |
|-----------|--------------|
| [Bina.az Real Estate Scraper & Database Sync](https://github.com/bahramzada/N8N-Projects/blob/main/Bina.az%20Real%20Estate%20Scraper%20%26%20Database%20Sync.json) | Scrapes real estate listings from Bina.az and syncs them with Google Sheets |
| [Glorri.az Job Scraper & Email Sender](https://github.com/bahramzada/N8N-Projects/blob/main/Glorri.az%20Job%20Scraper%20%26%20Email%20Sender.json) | Scrapes job postings from Glorri.az and sends daily updates via Gmail |
| _More workflows coming soon..._ | 🚀 |

---

## 🏗️ About Each Workflow

<a id="binaaz-real-estate-scraper--database-sync"></a>

### 🏠 Bina.az Real Estate Scraper & Database Sync

#### Overview
An automated workflow that continuously monitors and scrapes real estate listings from **[Bina.az](https://bina.az)** — a popular Azerbaijani real estate marketplace — and synchronizes the data with Google Sheets every **12 minutes**.  
It extracts detailed property data (description, stats, timestamps) and keeps an up-to-date structured database.

---

#### Workflow Purpose
**Goal:** Automate the collection, normalization, and synchronization of property listings.  
**Use Cases:**
- Track property market trends  
- Detect listing changes or removals  
- Maintain a historical dataset for analysis  
- Generate time-based reports  

---

#### Workflow Architecture

**Phase 1: Trigger & Data Fetch**
1. **Schedule Trigger** – runs every 12 minutes  
2. **HTTP Request (GraphQL)** – fetches latest listings  
3. **Extract Items (Code Node)** – flattens nested GraphQL response  

**Phase 2: HTML Parsing**
4. **HTTP Request (GET HTML)** – loads each property page  
5. **HTML Node (Description)** – extracts `.product-description__content`  
6. **HTML Node (Stats)** – extracts `.product-statistics__i-text`  

**Phase 3: Data Transformation**
7. **Code Node (JavaScript)** – parses and normalizes statistics  
   - Converts timestamps (local, UTC, Baku)  
   - Extracts numeric data via regex  
   - Outputs structured JSON  

**Phase 4: Merge & Store**
8. **Merge Node** – combines description, stats, and base data  
9. **Google Sheets (Upsert Row)** – syncs to main database (`bina.az`)  
10. **Google Sheets (Append/Update)** – updates secondary sheet (`Sayfa1`)  

---

#### Data Flow Summary
```plaintext
Schedule Trigger (12 min)
    ↓
Fetch Listings (GraphQL)
    ↓
Extract Items
    ↓
Fetch HTML Page
    ↓
┌──────────────────────────────┐
│ Extract Description & Stats  │
└──────────────────────────────┘
    ↓
Transform Data (JS)
    ↓
Merge All Fields
    ↓
┌──────────────────────────────┐
│ Google Sheets (Upsert/Sync)  │
└──────────────────────────────┘
```

---

<a id="glorriaz-job-scraper--email-sender"></a>

### 💼 Glorri.az Job Scraper & Email Sender

#### Overview
An automated n8n workflow that collects new job postings from **[Glorri.az](https://glorri.az)** several times a day and keeps a structured **Data Table (glorri)** synchronized.  
At the same time, it automatically **emails the latest data-related job postings** to a specified address.

---

#### Workflow Purpose
**Goal:** Automate the scraping and notification of new job openings — especially data-related ones.  
**Use Cases:**
- Receive email alerts for new data-related positions  
- Maintain an up-to-date local or cloud dataset  
- Track changes and trends in the job market  

---

#### Workflow Architecture

**Phase 1: Trigger & Fetch**
1. **Schedule Trigger** – runs **5 times daily (10:00, 13:00, 15:00, 17:00, 19:00)**  
2. **HTTP Request (Glorri API)** – fetches the 20 most recent job listings  

**Phase 2: Filtering & Extraction**
3. **Code Node (Filter)** – keeps only jobs containing `"data"` or `"anali"` in the title  
4. **Code Node (Extract)** – retrieves job `title` and `slug` fields  

**Phase 3: Storage & Email Notification**
5. **Data Table (Upsert Rows)** – inserts or updates job records in table `glorri.az`  
6. **Gmail Node** – sends an automatic email with the list of new postings  
   - Includes title, date, and direct link to each job  
   - Automatically adjusts timestamp to **Baku Time (UTC+4)**  

---

#### Data Flow Summary
```plaintext
Schedule Trigger (10:00, 13:00, 15:00, 17:00, 19:00)
    ↓
Fetch Glorri.az Listings (API)
    ↓
Filter Data-Related Jobs
    ↓
Extract Job Titles & Slugs
    ↓
┌────────────────────────────┐
│ Upsert into Data Table     │
└────────────────────────────┘
    ↓
┌────────────────────────────┐
│ Send Email Notification    │
└────────────────────────────┘
```

---

#### Key Features
- **Automated execution** several times per day  
- **Smart filtering** for relevant job titles  
- **Database sync** with upsert logic (no duplicates)  
- **Email summary** of latest positions with timestamps  
