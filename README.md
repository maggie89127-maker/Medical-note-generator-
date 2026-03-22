# 🏥 學習病歷產生器

將門診紀錄、急診紀錄或標準化病人腳本，轉換為結構化學習病歷，並提供臨床學習重點與 AI 即時問答。

---

## 功能總覽

| 功能 | 說明 |
|------|------|
| 📝 **學習病歷** | 根據輸入資料產生完整住院病歷（Chief Complaint → Plan） |
| 🔪 **內科 / 外科模式** | 內科完整版、外科精簡版，一鍵切換 |
| 🧩 **分段卡片** | 病歷拆為 8 個獨立區塊，各自獨立顯示 |
| 📋 **一鍵複製** | 每個區塊都有複製按鈕 |
| 🔄 **單段重生 (Redo)** | 不滿意某段？只重新生成該段，其餘不動 |
| 📏📐 **精簡版 / 完整版** | Present Illness、Impression、Plan 可切換精簡或完整版本，顯示在原版下方 |
| 🔴 **紅字標示** | ROS 與 PE 更動項目自動紅色字體 |
| ✳️ **ROS 異常提示** | ROS 有異常的項目以 `*` 紅字標示於值前方，例如 `fever:( *yes)` |
| 📚 **臨床學習重點** | 鑑別診斷機率排序、藥物劑量、Guideline 檢查、EBM 參考 |
| 📖 **Evidence-Based Guideline** | 引用 IDSA/AHA/ESC/NCCN/ADA 等國際指引，含年份與台灣差異 |
| 🔎 **AI 自我查核** | 自動檢查病歷內部一致性與學習重點正確性 |
| 💬 **AI 問答** | 根據病歷內容即時提問，支援多輪對話 |
| 🤖 **多模型支援** | Claude / OpenAI / Gemini 三選一 |
| 🛡️ **去識別化** | 自動遮蔽姓名、身分證字號、電話號碼 |
| ⚠️ **安全提示 + 免責聲明** | 輸入區警告 + 頁尾免責聲明（含 token 費用提醒） |

---

## 安裝與執行

```bash
pip install -r requirements.txt
streamlit run app.py
```

---

## 使用方式

1. 左側欄選擇 AI 模型 → 輸入對應 API Key
2. 貼入病患資料（可選填補充資料）→ 按「🩺 產生學習病歷」或「🔪 產生外科病歷」
3. 右側三個分頁：

| 分頁 | 功能 |
|------|------|
| 📝 學習病歷 | 8 段分段卡片，每段可 📋複製、🔄Redo、📏精簡/📐完整 |
| 📚 學習重點 | 診斷推理、DDx 排序表、藥物劑量、Guideline、EBM 參考、AI 自我查核 |
| 💬 AI 問答 | 自由提問，多輪對話 |

---

## 病歷格式規則

### Plan 與 Impression 共用格式（純文字 · 空白 + 短橫線）

Plan 與 Impression 區塊統一使用**純文字格式**，不使用任何 Markdown 語法。

格式規則：

- 所有列點一律使用 `" - "`（空白 + 短橫線 + 空白）開頭
- 不可使用 `"- "` 直接開頭（無前導空白）
- 不可使用 `"*"`、`"•"`、`"·"` 或數字編號列表（`1. 2. 3.`）
- 每一條內容獨立一行，維持同一層級（不可有縮排或子項目）
- 換行前不加入任何空格（無行尾空白）
- 同一診斷的所有資訊以分號 `;` 分隔寫在同一行

即使 AI 模型輸出標準 `"- "`、數字列表或其他格式，後處理程式會自動轉換為 `" - "` 格式。

### Plan 正確範例

```
Diagnostic:
 - Monitor renal function and electrolytes daily
 - Follow up blood culture results

Therapeutic:
 - Continue IV antibiotics
 - Fluid resuscitation with normal saline

Measurable goal:
 - Achieve afebrile status within 48 hours
 - Stabilize renal function within 5 days
```

### Impression 正確範例

```
 - Gouty arthritis with suspected secondary infection, r/o septic arthritis; Antibiotics: Ceftriaxone (D1: 2025/12/27), Teicoplanin (D1: 2025/12/27)
 - Acute kidney injury on CKD stage 4 (eGFR 29)
 - Leukocytosis with left shift

<Underlying status>
 - HTN, under medication control
 - CKD stage 4
```

### Review of System (ROS)

異常項目以紅色 `*` 標示於值前方，例如：`fever:( *yes)`。未更動項目維持原樣：`cough:( no)`。

### Physical Examination (PE)

異常發現以紅字標示（無 `*` 前綴）。

---

## 學習重點包含內容

- 📌 **主要診斷與臨床推理**
- 🔍 **鑑別診斷排序表** — 依機率排序，Markdown 表格呈現
- 💊 **治療藥物與劑量** — 藥名、劑量、途徑、頻率（含腎功能劑量調整）
- ⚠️ **Guideline 符合度檢查**
- 📖 **Evidence-Based Guideline 參考** — IDSA/AHA/ESC/NCCN/ADA（含年份、台灣差異）
- 🔬 **建議進一步檢查**
- ⚡ **學習重點摘要**
- 🔎 **AI 自我查核** — 病歷一致性 + 學習重點正確性

---

## 去識別化處理

| 類型 | 處理方式 |
|------|---------|
| 台灣身分證字號 | → `X000000000` |
| 手機號碼 | → `09XX-XXX-XXX` |
| 市話號碼 | → `0X-XXXX-XXXX` |
| 姓名 | → `XXX` |
| 病歷號 | → `XXXXXXXX` |

---

## 支援模型

| 模型 | 引擎 |
|------|------|
| Claude Sonnet 4 | Anthropic API（預設） |
| GPT-4o | OpenAI API |
| Gemini 2.0 Flash | Google AI |

---

## 色彩標示說明

| 標記 | 說明 |
|------|------|
| 🔴 紅色 | ROS / PE 更動項目 |
| `*` 紅色 | ROS 異常項目前方提示（例：`*yes`） |
| 🔵 藍色 | 補充資料內容（僅 Present Illness） |

---

## 部署

上傳 `app.py` + `requirements.txt` 至 GitHub → [share.streamlit.io](https://share.streamlit.io)
