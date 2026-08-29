<div align="center">

# PAD Payment PDF Splitter & Sender

[![Power Automate Desktop](https://img.shields.io/badge/Power%20Automate-Desktop-0066FF?style=flat-square&logo=microsoftpowerautomate&logoColor=white)](https://powerautomate.microsoft.com/)
[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%7C%2011-0078D6?style=flat-square&logo=windows&logoColor=white)](#)
[![M365 Outlook](https://img.shields.io/badge/M365-Outlook-0078D4?style=flat-square&logo=microsoftoutlook&logoColor=white)](https://www.microsoft.com/microsoft-365)
[![Microsoft Excel](https://img.shields.io/badge/Microsoft-Excel-217346?style=flat-square&logo=microsoftexcel&logoColor=white)](https://www.microsoft.com/microsoft-365/excel)
[![Workflow](https://img.shields.io/badge/Workflow-Enterprise%20RPA-success?style=flat-square)](#)
[![Data Privacy](https://img.shields.io/badge/Data%20Privacy-Sanitized-green?style=flat-square)](#-備註)

<br/>

🌐 **[繁體中文 (Traditional Chinese)](README.md)** | **[English](README_EN.md)**

</div>

基於 **Power Automate Desktop (PAD)** 與 **Microsoft 365 Outlook** 開發的 RPA 流程。目前實際應用於集團各子公司之間，用於自動化拆分批次付款清冊 PDF，並將個人付款通知單精準發送至對應員工公務信箱。

---

## 📌 專案背景與效益

集團每月財務結算時會產出包含所有員工的合併付款清冊（PDF）。過往需人工逐頁拆檔、核對工號通訊錄並手動發信，不僅耗時且存在寄錯個資的風險。

本流程將此作業完全自動化，目前已在集團內部子公司間穩定運行，單次批次處理時間由原本 30 分鐘縮減至 30 秒內，且達成 0% 寄送失誤率。

---

## ⚙️ 核心運作流程

```mermaid
graph LR
    A["掃描各子公司目錄"] --> B["載入通訊錄 Excel"]
    B --> C["逐頁拆分批次 PDF"]
    C --> D["RegEx 提取工號 (E\d{5})"]
    D --> E["匹配 Email"]
    E --> F["Outlook 發送附件"]
```

* **多子公司動態掃描**：自動偵測 `C:\PAD_Test\` 下的各公司資料夾，新子公司只需建立資料夾與通訊錄即可自動納入排程。
* **PDF 拆分與工號擷取**：逐頁拆分 PDF 並透過正規表達式 `E\d{5}` 辨識工號，自動依來源檔名與工號重命名（`<DocName>_<EmpID>.pdf`）。
* **資料隔離與精準寄件**：各公司通訊錄獨立於記憶體中比對，直接透過本機 M365 Outlook 發送，避免跨公司資料混淆與明文密碼暴露。
* **例外容錯機制**：使用 `ON ERROR -> GOTO NextPdf` 自動判定 PDF 結尾，確保批次作業不因超出頁數中斷。

---

## 📁 資料夾結構規範

```text
C:\PAD_Test\
├── Company_A\
│   ├── payment_batch_01.pdf   # 原始合併 PDF
│   ├── employee_map.xlsx      # 該公司員工通訊錄 (欄位: EmpId, Email)
│   └── Output\                # 自動拆分後的個人 PDF (例: payment_batch_01_E00001.pdf)
└── Company_B\
    ├── payment_batch_02.pdf
    ├── employee_map.xlsx
    └── Output\
```

---

## 🚀 快速使用 (PAD 匯入)

1. 在 **Power Automate Desktop** 建立新流程。
2. 開啟 [`src/flow.robin`](src/flow.robin)，全選複製（`Ctrl + A` ➔ `Ctrl + C`）。
3. 在 PAD 編輯畫布中直接貼上（`Ctrl + V`）即可載入完整步驟。
4. 依目錄結構在 `C:\PAD_Test\` 建立資料夾並放入檔案（可參考 `sample_data/` 測試資料）即可執行。

---

## 🔒 備註
本公開儲存庫中的程式碼與測試資料均已完成脫敏處理，不含任何企業真實個資與帳密資訊。
