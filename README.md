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
| [Glorri.az Job Scraper & Email Sender](#) | Scrapes job postings from glorri.az and sends them via email (coming soon) |
| _More workflows coming soon..._ | 🚀 |

---

## 🏗️ About Each Workflow

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
