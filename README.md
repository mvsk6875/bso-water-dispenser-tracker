# 🚰 BSO Water Dispenser Tracker

> An enterprise digital asset management system built with **AppSheet** and **Google Workspace**, designed to track water dispenser assets, automate contract lifecycle alerts, manage maintenance schedules, and monitor operational costs across multiple facilities.

---

## 📌 Project Overview

The **BSO Water Dispenser Tracker** centralizes tracking across multi-location facilities (covering Coway, Cuckoo, and TNB-owned machines). It transitions manual asset record-keeping into an automated digital platform featuring dynamic status dashboards, contract alerts, and financial tracking.

* **Platform:** AppSheet (No-Code Enterprise Application)
* **Backend Data Source:** Google Sheets / Workspace
* **Data Scale:** 13 Tables, 639 Columns, 17 UX Views, 34 Actions, 28 Format Rules

---

## 🔥 Key Features

* **📦 Asset Lifecycle & Contract Management:**
  * Tracks 60-month lease transitions (Rental to Owned mode).
  * Auto-calculates asset age in months and days to contract expiry.
  * Auto-generates status tags (`ACTIVE`, `EXPIRING SOON`, `URGENT (30 DAYS)`, `TERMINATED`, `DISPOSED`).
* **💰 Financial & PO Tracking:**
  * Tracks Purchase Order (PO) numbers, PO values, and Goods Receipt / Service Acceptance (GR/SA) values.
  * Calculates total outstanding debt and tracks additional charges (e.g., filter replacement fees).
* **⚙️ Automated Workflows & Alerts:**
  * **Contract Expiry Warning:** Triggered 30 days prior to expiration.
  * **Ownership Transition Alert:** Triggered at 60 months.
  * **Payment Outstanding Alert:** Debt monitoring and tracking.
  * **Emergency & Maintenance Alerts:** Real-time breakdown notifications.
* **📱 On-Site Operations:**
  * Dynamic QR Code generation for instant scanner-based asset lookup.
  * Direct contact routing for state/station PICs, Heads of Station (HOS), and vendor hotlines.

---

## 🏗️ Data Architecture & Schema Highlights

| Table Name | Role / Description | Key Columns |
| :--- | :--- | :--- |
| `DATA COWAY CUCKOO` | Core asset ledger containing location, PO, financial, and condition details. | `SERIAL NUMBER` (Key), `MODEL` (Ref), `PO NUMBER`, `PAYMENT STATUS`, `CONDITION ASSET` |
| `MACHINE_MANUAL` | Reference table storing equipment specs, user manuals, and cover images. | `Manual_ID` (Key), `Brand`, `Model`, `Manual_File` |
| `FILTER` | Operational filtering view for multi-state and multi-vendor user access. | `Filter_ID` (Key), `Pilih_Vendor`, `Pilih_State`, `Status_Kontrak` |
| `Process State Tables` | AppSheet state engines handling notification alerts, monthly KPI reporting, and debt monitoring. | `Instance Id` (Key), `Asset ID`, `Contract_Status_VC` |

---

## 💡 Key AppSheet Formulas & Logic Used

### 1. Ownership Mode Calculation (Lease vs. Owned)
```excel
=IF(((YEAR(TODAY()) - YEAR([INSTALLATION DATE])) * 12 + (MONTH(TODAY()) - MONTH([INSTALLATION DATE]))) >= 60, "TNB Owned", "Rental")
```

### 2. Contract Status Automation (`Contract_Status_VC`)
```excel
=IFS(
  [CONDITION ASSET] = "DISPOSED", "DISPOSED",
  [CONDITION ASSET] = "MISSING", "MISSING",
  [STATUS] = "TERMINATED", "TERMINATED",
  [Days_To_Expiry] < 0, "COMPLETED",
  [Days_To_Expiry] <= 30, "URGENT (30 DAYS)",
  [Days_To_Expiry] <= 150, "EXPIRING SOON",
  TRUE, "ACTIVE"
)
```

### 3. Dynamic QR Code Generator for On-Site Scans
```excel
=CONCATENATE("[https://chart.googleapis.com/chart?chs=150x150&cht=qr&chl=](https://chart.googleapis.com/chart?chs=150x150&cht=qr&chl=)", [SERIAL NUMBER])
```

---

## 📂 Documentation

Full technical specs and generated system documentation are available in the [`/docs`](./docs/bso%20water%20dispenser%20tracker.pdf) directory.
