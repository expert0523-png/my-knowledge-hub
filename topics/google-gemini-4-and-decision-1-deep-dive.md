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
> **適用環境**：Google AI Studio / Vertex AI / Google Antigravity (v2.29 ~ v2.30+)

---

## 📌 一、 導讀與命名規則變革

進入 Gemini 4 世代後，Google 正式廢除了沿用多年的 `Flash`、`Pro`、`Ultra` 與 `Deep Think` 命名方式，全面改以**「化學元素（Hydrogen 氫、Argon 氬、Carbon 碳）」**來區分生成式模型的算力與推理等級；同時，針對高頻率的分類與路由需求，獨立出全新的非生成式決策模型產品線 **`Gemini Decision 1`**。

---

## 📊 二、 四大模型核心規格與對標總表

| 模型名稱 | Model ID | 發布日期 | API 定價 (每百萬 Tokens) | 上下文視窗 | 對標舊世代 Google 模型 | 對標外部競爭模型 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Gemini 4 Hydrogen**<br>*(氫)* | `gemini-4-hydrogen` | 2026/11/13 | 輸入: $1.50<br>輸出: $7.50<br>*(含 AI Studio 免費層)* | 1M (100萬) | **Gemini Flash 系列**<br>*(取代 3.8 / 2.5 Flash)* | GPT-6.1 Sol / Claude Haiku / DeepSeek V4.1 Flash |
| **Gemini 4 Argon**<br>*(氬)* | `gemini-4-argon` | 2026/10/14 | **推廣價 (11/30前)**:<br>輸入 $2.00 / 輸出 $10.00<br>*(原價 $4.00 / $20.00)* | 1M (100萬) | **Gemini Pro 系列**<br>*(取代 3.1 / 2.5 Pro)* | GPT-6 Astra / Claude Fable 5.1 (Sonnet 級) |
| **Gemini 4 Carbon**<br>*(碳)* | `gemini-4-carbon` | 2026/11/11 | **推廣價 (11/30前)**:<br>輸入 $2.00 / 輸出 $10.00<br>*(原價 $4.00 / $20.00)* | 1M (100萬) | **Gemini Ultra + Deep Think**<br>*(頂規深度推理)* | Claude Opus 5.5 / OpenAI o3-pro / GPT-6 頂規版 |
| **Gemini Decision 1**<br>*(決策 1 號)* | `gemini-decision-1` | 2026/11/12 | **輸入: $0.10**<br>**輸出: $0 (完全免費)** | 256K (26.2萬) | **Gemini Flash-Lite 系列**<br>*(取代 3.5 Flash-Lite 分類任務)* | OpenAI GPT-6 Luna / TypeSafe Jev 1.14 |

> **💡 Gemini 4 系列共通底層規格**：  
> `Hydrogen`、`Argon`、`Carbon` 三款模型皆具備 **100 萬 (1M) Tokens** 多模態統一上下文視窗（支援文字、圖片、音訊、影片、PDF 與大型程式碼庫），預設單次輸出上限為 **65,536 Tokens**，透過接續機制（Continuation）最高可連續輸出達 **100 萬 Tokens**。

---

## 🔍 三、 各模型深度解析

### 1. Gemini 4 Hydrogen (`gemini-4-hydrogen`) —— 高速高 CP 值的日常主力
* **定位**：取代過去的 Flash 系列，專為高速程式開發、高吞吐量代理工作流（Agentic workflows）與多模態知識工作設計。
* **核心亮點**：
  * **跨代「下剋上」的智力表現**：在預設 `high` 思考深度下，於 Artificial Analysis Intelligence Index 拿下 **51 分**（開啟 `xhigh` 可達 **54 分**），不僅輾壓上一代 `Gemini 3.8 Flash`（31 分），甚至微幅超越早一個月發布的標準旗艦 `Argon`（50 分）。
  * **終端機與代理能力暴增**：速度比 3.8 Flash 快 2.5 倍，Terminal-Bench 4.1 成績從 18.2% 躍升三倍至 **57%**。
  * **4 段思考深度控制**：支援 `low`、`medium`、`high`（預設）、`xhigh`。開發實務上建議維持預設 `high` 以兼顧品質與 Token 消耗。
* **適用場景**：日常 Vibe Coding、自動化網頁爬蟲、Google Drive 文件整理、多數常規 Remote Agent 任務。

### 2. Gemini 4 Argon (`gemini-4-argon`) —— 均衡穩健的專業旗艦
* **定位**：Gemini 4 世代首發的全能型前沿模型，完全繼承過去「Pro」系列的定位（Google AI Pro $19.99/月 方案主力）。
* **核心亮點**：
  * **推理深度與時間成本的最佳平衡**：專注於真實世界的複雜軟體工程、企業級知識工作（法律合約、財務報表分析）與機器人空間推理（Robotics）。
  * **避免過度思考（Overthinking）**：相較於 Carbon 會消耗大量思考 Token 進行極限驗證，Argon 能在提供深厚專業推理的同時，維持穩定的回應速度與合理的額度消耗。
* **適用場景**：長篇技術文件撰寫、複雜業務邏輯開發、需要高精準度但不想等待長時驗證的分析工作。

### 3. Gemini 4 Carbon (`gemini-4-carbon`) —— 挑戰極限的最強推理帝王
* **定位**：Google 目前最聰明、最強大的頂尖前沿模型（Frontier Model），結合過去 `Ultra` 與 `Deep Think` 的定位（Google AI Ultra $99.99/月 方案主力）。
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

## 🛠️ 四、 Google Antigravity 實戰配置與最佳實踐

在 Google Antigravity（特別是使用雲端沙盒 **Remote Agents** 功能時），除了直接指定上述模型外，強烈建議搭配官方新增的協調機制與工作流：

### 1. 善用第 5 個隱藏王牌：`Orchestrator`（動態雙向協調模式）
* **運作機制**：在 Antigravity 模型選單中選擇 `Orchestrator`，系統會在同一個任務中**自動於 `Carbon` 與 `Hydrogen` 之間動態雙向切換**。
  1. **規劃階段**：由 `Carbon`（大腦）以唯讀模式探索專案架構、釐清模糊需求並制定執行計畫。
  2. **執行階段**：自動交棒給 `Hydrogen`（快手）高速修改程式碼、安裝套件與執行測試。
  3. **除錯回退**：若 `Hydrogen` 遇到測試失敗或瓶頸，立即將控制權交回給 `Carbon` 診斷並修正計畫。
* **實測效益**：在 Terminal-Bench 測試中，成功率幾乎與全程使用 Carbon 持平，但**平均節省 24% 的額度（Quota）消耗，並加快 19% 的完成速度**。

### 2. 「G4 生成 + Decision 1 決策」的黃金架構組合
當你在 Antigravity 中請 Remote Agent 幫你開發個人自動化工具（如：Google Drive 帳單自動整理、新聞爬蟲篩選器、信箱分類器）時：
* **開發階段（在 Antigravity 介面）**：使用 `Orchestrator` 或 `Gemini 4 Hydrogen` 撰寫專案程式碼。
* **運行階段（寫進程式碼裡的 API 呼叫）**：明確要求 Agent 在程式碼的分類、過濾與判斷節點中，串接 **`gemini-decision-1` (Decisions API)**，而非呼叫生成式模型。這樣能讓打造出來的工具擁有毫秒級反應速度與趨近於零的運行成本。
