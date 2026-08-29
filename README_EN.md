<div align="center">

# PAD Payment PDF Splitter & Sender

🌐 **[繁體中文 (Traditional Chinese)](README.md)** | **[English](README_EN.md)**

<br/>

[![Power Automate Desktop](https://img.shields.io/badge/Power%20Automate-Desktop-0066FF?style=flat-square&logo=microsoftpowerautomate&logoColor=white)](https://powerautomate.microsoft.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%2011-0078D6?style=flat-square&logo=windows&logoColor=white)](#)
[![M365 Outlook](https://img.shields.io/badge/M365-Outlook-0078D4?style=flat-square&logo=microsoftoutlook&logoColor=white)](https://www.microsoft.com/microsoft-365)
[![Microsoft Excel](https://img.shields.io/badge/Microsoft-Excel-217346?style=flat-square&logo=microsoftexcel&logoColor=white)](https://www.microsoft.com/microsoft-365/excel)
[![Workflow](https://img.shields.io/badge/Workflow-Enterprise%20RPA-success?style=flat-square)](#)
[![Data Privacy](https://img.shields.io/badge/Data%20Privacy-Sanitized-green?style=flat-square)](#-note)

</div>

An RPA workflow developed with **Power Automate Desktop (PAD)** and **Microsoft 365 Outlook**. Currently applied in production across enterprise group subsidiaries to automate the splitting of consolidated batch payment PDF reports and dispatch personalized payment advice to employees.

---

## 📌 Background & Practical Value

During monthly financial settlement, consolidated payment proposal reports (PDF) containing all employee records are generated. Manually splitting pages, checking employee IDs, and drafting individual emails is time-consuming and carries a high risk of sending confidential payroll data to the wrong person.

This workflow fully automates the process across group subsidiaries, cutting processing time from 30 minutes to under 30 seconds per batch with zero delivery errors.

---

## ⚙️ Workflow Logic

```mermaid
flowchart TD
    subgraph Trigger["1. Trigger & Input"]
        T1["Scheduled / Manual RPA Trigger"] --> T2["Dynamic Directory Scan<br/>C:\\PAD_Test\\"]
        T2 --> T3["Load Employee Directory<br/>employee_map.xlsx"]
        T2 --> T4["Fetch Batch Payment Reports<br/>payment_batch_*.pdf"]
    end

    subgraph CoreEngine["2. Core Processing Engine (PAD / Robin)"]
        T3 & T4 --> P1["Split PDF Page-by-Page<br/>(Extract Pages)"]
        P1 --> P2["RegEx Employee ID Extraction<br/>E\\d{5}"]
        P2 --> P3["In-Memory Data Matching<br/>(DataTable Filter by EmpId)"]
        P1 -.->|Out of bounds / EOF| ERR["ON ERROR Handling<br/>Auto-skip to Next PDF"]
    end

    subgraph Output["3. Output & Delivery"]
        P3 --> O1["Auto-Rename Individual PDF<br/>&lt;DocName&gt;_&lt;EmpID&gt;.pdf"]
        P3 --> O2["M365 Outlook Targeted Dispatch<br/>(Attach Personalized Advice)"]
        ERR --> O3["Batch Completion / Execution Log"]
    end
```

* **Multi-Company Dynamic Scanning**: Discovers company subdirectories under `C:\PAD_Test\`. New entities are onboarded simply by adding their folder and Excel mapping file.
* **PDF Page Splitting & ID Extraction**: Extracts pages sequentially and extracts employee IDs using RegEx (`E\d{5}`), auto-renaming outputs to `<DocName>_<EmpID>.pdf`.
* **Data Isolation & Secure Dispatch**: Loads employee mappings into isolated in-memory tables per entity and sends notifications directly via local M365 Outlook.
* **Error Handling**: Uses `ON ERROR -> GOTO NextPdf` to detect end-of-document conditions smoothly without interrupting batch execution.

---

## 📁 Directory Standard

```text
C:\PAD_Test\
├── Company_A\
│   ├── payment_batch_01.pdf   # Source consolidated PDF
│   ├── employee_map.xlsx      # Email directory (Columns: EmpId, Email)
│   └── Output\                # Split individual PDFs (e.g., payment_batch_01_E00001.pdf)
└── Company_B\
    ├── payment_batch_02.pdf
    ├── employee_map.xlsx
    └── Output\
```

---

## 🚀 How to Run (PAD Import)

1. Create a new flow in **Power Automate for Desktop**.
2. Open [`src/flow.robin`](src/flow.robin), select all and copy (`Ctrl + A` ➔ `Ctrl + C`).
3. Paste (`Ctrl + V`) directly onto the PAD designer canvas.
4. Prepare folders under `C:\PAD_Test\` with your test files (see `sample_data/` for templates) and run.

---

## 🔒 Note
All sample data, employee names, and email domains in this public repository have been sanitized.
