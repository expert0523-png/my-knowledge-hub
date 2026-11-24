---
title: Xubuntu 安裝與遷移 Antigravity CLI (agy) 完整筆記
date: 2026-11-24
tags:
  - Xubuntu
  - Linux
  - Antigravity
  - agy
  - RemoteControl
  - 維運筆記
aliases:
  - Xubuntu agy 安裝教學
  - Snap 遷移 Standalone agy
---

# 🐧 Xubuntu 安裝與遷移 Antigravity CLI (`agy`) 完整筆記

> 本文檔已歸檔於知識庫 `topics/xubuntu-agy-installation-guide.md`，並永久同步於本地與雲端。

這份筆記整理了在 **Xubuntu** 上將 `agy` 從 **Snap 版本**更換為 **官方獨立安裝版（Standalone Binary）** 的完整流程，以及未來的遠端連線與自我更新方式。

---

## 一、 為什麼要從 Snap 換成官方獨立版？

| 比較項目 | Snap 版 (`/snap/antigravity-cli/...`) | 官方獨立版 (`~/.local/bin/agy`) |
| :--- | :--- | :--- |
| **檔案系統權限** | 掛載於唯讀的 SquashFS，無法直接修改執行檔 | 位於個人家目錄下，擁有完整讀寫權限 |
| **系統工具存取** | 受 Snap 沙盒限制（例如無法在對話中呼叫 `/usr/bin/sudo`） | 與一般終端機環境相同，無額外沙盒阻擋 |
| **更新方式** | 必須在主機終端機執行 `sudo snap refresh antigravity-cli` | 可直接在本地或**遠端對話中**執行 `agy update` 免密碼更新 |
| **設定檔路徑** | `~/snap/antigravity-cli/common/.gemini/antigravity-cli` | `~/.gemini/antigravity-cli` |

> [!TIP]
> 換成官方獨立版後，即使人在外面使用 Antigravity 遠端連線回家裡的 Xubuntu，也能隨時讓 Agent 直接執行 `agy update` 完成更新。

---

## 二、 完整遷移與安裝步驟（回家後在 Xubuntu 終端機執行）

### 步驟 1：停止目前執行中的遠端服務並遷移設定檔
Snap 版本將你的登入憑證、設定檔（`settings.json`）、自訂 Skills 與對話紀錄存放在 `~/snap/antigravity-cli/common/.gemini/antigravity-cli`。在移除 Snap 前，先將它們複製到標準路徑 `~/.gemini/antigravity-cli`，即可免去重新設定與重新登入的麻煩：

```bash
# 1. 若有執行中的 remote-control 背景服務，先將其停止
agy remote-control stop 2>/dev/null || true

# 2. 建立標準設定目錄並將現有設定與紀錄完整複製過去
mkdir -p ~/.gemini
cp -a ~/snap/antigravity-cli/common/.gemini/antigravity-cli ~/.gemini/
```

---

### 步驟 2：移除 Snap 版的 `agy`
確認設定檔已備份到 `~/.gemini/antigravity-cli` 後，即可移除 Snap 套件：

```bash
sudo snap remove antigravity-cli
```

---

### 步驟 3：使用官方腳本安裝獨立版 `agy`
執行 Google Antigravity 官方的一鍵安裝腳本：

```bash
curl -fsSL https://antigravity.google/cli/install.sh | bash
```

這個腳本會自動完成以下動作：
1. 偵測你的 Linux 系統架構（如 `x86_64`）。
2. 下載最新穩定版 `agy` 執行檔並安裝至 `~/.local/bin/agy`。
3. 自動檢查並將 `~/.local/bin` 加入你的 `~/.bashrc` 環境變數 `PATH` 中。

---

### 步驟 4：重新載入環境並驗證安裝
安裝完成後，重新載入 Shell 設定並確認路徑與版本：

```bash
# 重新載入 bashrc（並清除 bash 對舊路徑的快取）
source ~/.bashrc
hash -r

# 確認執行檔路徑是否已變更為 ~/.local/bin/agy
which agy

# 確認版本號
agy --version
```

> [!NOTE]
> 如果執行 `which agy` 沒有顯示 `~/.local/bin/agy`，可以使用 `agy install` 指令讓 CLI 自動幫你配置好環境變數與路徑，或手動在 `~/.bashrc` 最後加上一行：
> ```bash
> export PATH="$HOME/.local/bin:$PATH"
> ```

---

## 三、 日常維護、遠端連線與更新指令速查

完成遷移後，未來在 Xubuntu 上的常用操作如下：

### 1. 自我更新（本地或遠端皆可）
在終端機直接輸入，或在 Antigravity 對話中請 Agent 幫你執行：
```bash
agy update
```
- `agy` 會直接下載新版執行檔並覆蓋 `~/.local/bin/agy`，**不需要 `sudo` 密碼**。
- 更新完成後，新開的對話或重啟 `remote-control` 後即會套用最新版本。

### 2. 遠端控制服務（Remote Control）管理
如果你習慣在出門前讓家裡的 Xubuntu 保持遠端連線待命：
```bash
# 啟動背景遠端連線 daemon
agy remote-control start

# 查看目前遠端連線服務的狀態
agy remote-control status

# 停止遠端連線服務
agy remote-control stop

# 若想直接啟動互動式 CLI 並同時開啟遠端控制
agy --remote-control
```

> [!IMPORTANT]
> **遠端更新後如何讓背景服務套用新版？**
> 當你在遠端請 Agent 執行 `agy update` 更新完執行檔後，若要讓背景的 `remote-control` 服務也切換到新版本，只需執行：
> ```bash
> agy remote-control stop && agy remote-control start
> ```
> （若在遠端對話中讓 Agent 執行重啟，連線會短暫重連一次，重連後即為最新版本。）

---

## 四、 重要檔案與目錄位置參考

安裝完成後，所有相關檔案都會集中於你的個人家目錄下，方便管理與備份：

- **執行檔位置**：`~/.local/bin/agy`
- **全域設定檔**：`~/.gemini/antigravity-cli/settings.json`
- **自訂 Skills / Rules / 對話紀錄**：`~/.gemini/antigravity-cli/`
- **預設暫存專案目錄（Scratch）**：`~/.gemini/antigravity-cli/scratch/`
