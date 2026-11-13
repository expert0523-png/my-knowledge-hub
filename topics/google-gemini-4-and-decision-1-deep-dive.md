---
title: Google Gemini 4 世代與 Decision 1 模型全解析報告
date: 2026-11-13
tags:
  - GoogleAI
  - Gemini4
  - GeminiDecision
  - Antigravity
  - LLM
  - 模型評測
aliases:
  - Gemini 4 系列解析
  - Gemini Decision 1
---

# 🚀 Google Gemini 4 世代與 Decision 1 模型全解析報告

> **最後更新日期**：2026 年 11 月 13 日  
> **適用環境**：Google AI Studio / Gemini API / Google Antigravity (v2.29 ~ v2.31+)

---

## 📌 一、 導讀與命名規則變革

進入 Gemini 4 世代後，Google 正式廢除了沿用多年的 `Flash`、`Pro`、`Ultra` 與 `Deep Think` 命名方式，全面改以**「化學元素（Hydrogen 氫、Argon 氬、Carbon 碳）」**來區分生成式模型的算力與推理等級；同時，針對高頻率的分類、審核與路由需求，獨立出全新的非生成式決策模型產品線 **`Gemini Decision 1`**。

---

## 📊 二、 四大模型核心規格與對標總表

| 模型名稱 | Model ID | 發布日期 | API 定價 (每百萬 Tokens) | 上下文視窗 | 對標舊世代 Google 模型 | 對標外部競爭模型 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Gemini 4 Hydrogen**<br>*(氫)* | `gemini-4-hydrogen` | 2026/11/13 | 輸入: $1.50<br>輸出: $7.50<br>*(含 AI Studio 免費層)* | 1M (100萬) | **Gemini Flash 系列**<br>*(取代 3.8 / 2.5 Flash)* | GPT-6.1 Sol / Claude Haiku / DeepSeek V4.1 Flash |
| **Gemini 4 Argon**<br>*(氬)* | `gemini-4-argon` | 2026/10/14 | **推廣價 (11/30前)**:<br>輸入 $2.00 / 輸出 $10.00<br>*(原價 $4.00 / $20.00)* | 1M (100萬) | **Gemini Pro 系列**<br>*(取代 3.1 / 2.5 Pro)* | GPT-6 Astra / Claude Fable 5.1 (Sonnet 級) |
| **Gemini 4 Carbon**<br>*(碳)* | `gemini-4-carbon` | 2026/11/11 | **推廣價 (11/30前)**:<br>輸入 $2.00 / 輸出 $10.00<br>*(原價 $4.00 / $20.00)* | 1M (100萬) | **Gemini Ultra + Deep Think**<br>*(頂規深度推理)* | Claude Opus 5.5 / OpenAI o3-pro / GPT-6 頂規版 |
| **Gemini Decision 1**<br>*(決策 1 號)* | `gemini-decision-1` | 2026/11/12 | **輸入: $0.10**<br>**輸出: $0 (完全免費)** | 256K (26.2萬) | **Gemini Flash-Lite 系列**<br>*(取代 3.5 Flash-Lite 分類任務)* | OpenAI GPT-6 Luna / TypeSafe Jev 1.14 |

> **💡 Gemini 4 系列（Hydrogen / Argon / Carbon）共通底層規格**：  
> 三款生成式模型皆具備 **100 萬 (1M) Tokens** 多模態統一上下文視窗（支援文字、圖片、音訊、影片、PDF 與大型程式碼庫），預設單次輸出上限為 **65,536 Tokens**，透過接續機制（Continuation）最高可連續輸出達 **100 萬 Tokens**。

---

## 🔍 三、 四大模型深度解析

### 1. Gemini 4 Hydrogen (`gemini-4-hydrogen`) —— 高速高 CP 值的日常主力
* **定位**：取代過去的 Flash 系列，專為高速程式開發、高吞吐量代理工作流（Agentic workflows）與多模態知識工作設計。
* **核心亮點**：
  * **跨代「下剋上」的智力表現**：在預設 `high` 思考深度下，於 Artificial Analysis Intelligence Index 拿下 **51 分**（開啟 `xhigh` 可達 **54 分**），不僅輾壓上一代 `Gemini 3.8 Flash`（31 分），甚至微幅超越早一個月發布的標準旗艦 `Argon`（50 分）。
  * **終端機與代理能力暴增**：速度比 3.8 Flash 快 2.5 倍，Terminal-Bench 4.1 成績從 18.2% 躍升三倍至 **57%**。
  * **4 段思考深度控制**：支援 `low`、`medium`、`high`（預設）、`xhigh`。開發實務上建議維持預設 `high` 以兼顧品質與 Token 消耗。
* **適用場景**：日常 Vibe Coding、自動化網頁爬蟲、Google Drive 文件整理、多數常規 Remote Agent 執行任務。

### 2. Gemini 4 Argon (`gemini-4-argon`) —— 均衡穩健的專業旗艦
* **定位**：Gemini 4 世代首發的全能型前沿模型，完全繼承過去「Pro」系列的定位（為 Google AI Pro 方案之頂規模型）。
* **核心亮點**：
  * **推理深度與時間成本的最佳平衡**：專注於真實世界的複雜軟體工程、企業級知識工作（法律合約、財務報表分析）與機器人空間推理（Robotics）。
  * **避免過度思考（Overthinking）**：相較於 Carbon 會消耗大量思考 Token 進行極限驗證，Argon 能在提供深厚專業推理的同時，維持穩定的回應速度與合理的額度消耗。
* **適用場景**：專案架構規劃、長篇技術文件撰寫、複雜業務邏輯開發、需要高精準度但不想等待長時驗證的分析工作。

### 3. Gemini 4 Carbon (`gemini-4-carbon`) —— 挑戰極限的最強推理帝王
* **定位**：Google 目前最聰明、最強大的頂尖前沿模型（Frontier Model），結合過去 `Ultra` 與 `Deep Think` 的定位。
* **核心亮點**：
  * **延伸驗證迴圈（Extended Verification Loops）**：面對極端難題時，願意投入大量思考 Token 進行徹底探索與自我驗證。於 Artificial Analysis 拿下 **60 分**，並在 CursorBench 4.0、Perplexity WANDR 與 Zapier AutomationBench 奪冠。
  * **頂尖科學與資安實績**：曾驅動代理團隊花費 4 週成功在數學上形式化驗證 Signal 加密協定，並於 Project Fortify 中挖掘出真實世界的 0-day 漏洞。
  * **Antigravity 專屬 `/swarm`（蜂群協作）**：在 Antigravity 中可擔任總指揮，於隔離的 Git worktrees 中平行派發多個子代理（Workers）寫程式，並配置「對抗性驗證員（Adversarial verifiers）」進行嚴格測試。
* **適用場景**：跨語言/跨框架大型專案重構、極難重現的 Bug 根因分析、0-day 資安審計、長時運行的無人值守任務（Long-horizon tasks）。

### 4. Gemini Decision 1 (`gemini-decision-1`) —— 毫秒級多模態決策裁判
* **定位**：搭配全新 **Gemini Decisions API** 推出的首款非生成式「決策模型（Decision Model）」。**它不生成自然語言文字**，而是針對固定選項在一次前向運算（Single forward pass）中直接給出機率與校準信心分數（0～1）。
* **核心亮點**：
  * **破壞性價格與極致低延遲**：輸入每百萬 Tokens 僅 **$0.10 美元**，**輸出完全免費（$0）**，成本比過去最便宜的 `Gemini 3.5 Flash-Lite` 低近 5 倍。16K Tokens 內端到端延遲僅 **150～250 毫秒（0.15～0.25 秒）**。
  * **三大決策題型**：
    1. **是非題（Yes/No）**：條件成立判斷、合規檢查、安全過濾。
    2. **多選一（Multiple Choice）**：支援 2～255 個選項分類與 Agent 工具路由（Routing）。
    3. **依量表評分（Rubric Scoring）**：依自訂標準進行多維度打分（如 1～5 分評估嚴重度）。
  * **單次批次處理 1,000 題**：支援多模態輸入（文字、圖片、音訊、影片、PDF），可上傳一次檔案並在單次 API 請求中同時評估最多 **1,000 個獨立問題**，大幅降低自動化流程成本。
* **適用場景**：自動化腳本中的信件/發票分類、客服意圖分流（Routing）、大規模多模態內容審核、RAG 檢索結果重排序。

---

## 🛠️ 四、 Google Antigravity 實戰配置指南（依訂閱方案區分）

在 Google Antigravity（包含桌面版與雲端沙盒 **Remote Agents**）中，個人帳號（@gmail.com）會依據訂閱方案（Pro 或 Ultra）看到不同的模型選單，建議採用對應的最佳實踐：

### 1. Google AI Pro 用戶 ($19.99/月)：善用「Argon + Hydrogen 雙模型手動接力」
Pro 方案在 Antigravity 選單中可使用 **`Gemini 4 Argon`** 與 **`Gemini 4 Hydrogen`**（不包含 Carbon 與內建 Orchestrator 選項）。為避免單純使用 Argon 快速耗盡額度（Quota），建議在同一個 Session 中手動切換接力：
* **Step 1（架構規劃與除錯）**：先選擇 **`Gemini 4 Argon`**，要求 Agent 以唯讀方式探索程式碼庫、釐清需求並產出詳細的實作計畫（Implementation Plan）。
* **Step 2（高速寫碼與測試）**：計畫確認後，於同一對話框將模型切換為 **`Gemini 4 Hydrogen`**（維持預設 `high` 思考深度），讓它根據上述計畫快速修改程式碼、安裝套件與執行測試，大幅節省 Pro 額度並提升執行速度。

### 3. Google AI Ultra 用戶 ($99.99/月起)：啟用內建 `Orchestrator` 與 `/swarm`
Ultra 方案擁有完整 **`Gemini 4 Carbon`** 權限，並在 Antigravity v2.31.2+ 模型選單中獨享 **`Orchestrator`（動態雙向協調模式）**：
* **自動雙向切換**：選擇 `Orchestrator` 後，系統會在背景自動由 **Carbon** 負責規劃與卡關時的診斷，並自動交棒給 **Hydrogen** 負責高速執行與跑測試。實測成功率與全程使用 Carbon 相當，但可節省 **24% 額度消耗**並加快 **19% 完成速度**。
* **重度任務召喚蜂群**：面對超大型重構時，可選擇 Carbon 並輸入 `/swarm` 啟動多代理平行協作。

### 3. API 開發黃金組合：「G4 寫程式 + Decision 1 做判斷」
當你在 Antigravity 中請 Remote Agent 幫你撰寫個人自動化小工具（如：Google Drive 帳單自動整理、新聞爬蟲篩選器、信箱分類器）時：
* **開發階段（Antigravity 介面）**：用 `Hydrogen` 或 `Argon` 幫你寫出 Python / Node.js 腳本。
* **運行階段（寫進程式碼裡的 API）**：在 Prompt 中明確指示 Agent：「*請在程式碼的分類、過濾與是否判斷節點中，串接最新的 **`gemini-decision-1` (Decisions API)** 來執行，不要呼叫生成式模型*」。如此打造出來的自動化工具，不僅反應速度在毫秒等級，日常運行的 API 帳單成本也幾乎趨近於零！
