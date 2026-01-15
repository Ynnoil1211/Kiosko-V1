# 🍽️ RestoGuard Kiosk: Serverless Time & Attendance System

> A robust, offline-first **Time & Attendance (T&A)** solution designed for high-turnover retail and hospitality environments. Built on a serverless architecture using Google Ecosystem.

![Version](https://img.shields.io/badge/version-1.0.0-blue?style=flat-square)
![Architecture](https://img.shields.io/badge/architecture-serverless-orange?style=flat-square)
![Status](https://img.shields.io/badge/status-production--ready-green?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-lightgrey?style=flat-square)

## 📖 Executive Summary

**RestoGuard Kiosk** is a lightweight, cost-effective attendance tracking system designed to replace legacy biometric hardware and subscription-based SaaS platforms. It is engineered to operate in **high-latency environments** (unstable internet) by leveraging a "Store-and-Forward" architecture.

The system handles complex workforce scenarios, including **cross-midnight shifts** (night shifts), split shifts, and automated payroll calculation, utilizing Google Sheets as a relational database and Google Apps Script as the backend API.

## 🚀 Key Technical Features

### 🛡️ Fault Tolerance & Offline Persistence
* **Store-and-Forward Architecture:** Implements a local queuing system (`localStorage`) that captures transactions during network outages. Synchronization occurs asynchronously once connectivity is restored.
* **Resiliency:** The frontend autonomously manages connection states (Online/Offline) without user intervention.

### 🔒 Data Integrity & Security
* **Idempotency Enforcement:** Utilizes client-side UUID generation for every transaction. This prevents duplicate records caused by network retries or "double-click" user errors.
* **Concurrency Handling:** The backend implements `LockService` to prevent race conditions during simultaneous write operations to the database.
* **Auditability:** Immutable logging of both Client Timestamp (local) and Server Timestamp (source of truth).

### 🧠 Advanced Business Logic
* **Midnight Crossover Logic:** Algorithms intelligently group entry/exit markers that span across two calendar days (e.g., 10:00 PM to 02:00 AM) into a single logical shift.
* **Automated Payroll Processing:** A backend cron job processes raw logs to calculate net work hours, deducting break times based on customizable business rules.
* **Anomaly Detection:** Automatically flags operational errors such as "Missing Intermediate Clock-out" or "Unclosed Shifts."

## 🛠️ System Architecture

The project follows a **Client-Serverless** pattern:

1.  **Frontend (Kiosk):**
    * **Stack:** HTML5, CSS3, Vanilla JavaScript (ES6+).
    * **Role:** User Interface, Input Validation, Offline Queue Management, UUID Generation.
2.  **Transport Layer:**
    * `fetch` API sending JSON payloads to a Webhook endpoint.
3.  **Backend (API):**
    * **Stack:** Google Apps Script (GAS).
    * **Role:** Request validation, Idempotency checks (CacheService), Data persistence.
4.  **Data Layer:**
    * **Stack:** Google Sheets.
    * **Structure:** Configuration Table, Immutable Ledger (Raw Logs), Processed Payroll Views.

## 📂 Project Structure

```bash
/
├── src/
│   ├── client/
│   │   └── index.html      # The Kiosk Interface (Single Page Application)
│   └── server/
│       └── code.gs         # Backend logic (Deploy to Google Apps Script)
├── docs/
│   └── architecture.png    # System diagrams
└── README.md
