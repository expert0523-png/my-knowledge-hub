---
title: Windows 11 2026年11月累積更新 (KB5132061) 與近期資安維運筆記
date: 2026-11-12
tags:
  - Windows11
  - WindowsUpdate
  - KB5132061
  - SMBv1
  - NTLM
  - 資安維運
aliases:
  - KB5132061
  - 2026-11 Windows Update
---

# 🪟 Windows 11 2026年11月更新 (KB5132061) 維運筆記

> [!abstract] 更新基本資訊
> - **發佈日期**：2026-11-10 (Patch Tuesday)
> - **更新編號**：`KB5132061`（約 5 GB，修補 700+ 個 Windows 漏洞）
> - **對應版本與 Build 號**：
>   - **Win11 26H2**：Build `26300.9974`
>   - **Win11 25H2**：Build `26200.9974`
>   - **Win11 24H2**：Build `26100.9974` *(⚠️ 僅限企業版/教育版；家用/專業版已於 2026/10 EOL)*
> - **同批次其他系統 KB 對照**：
>   - Win11 23H2：`KB5131087` | Win10 22H2：`KB5131096`
>   - Server 2025：`KB5131080` | Server 2022：`KB5131090`
>   - Server 2019：`KB5131085` | Server 2016：`KB5131291`

---

## 🚨 一、 SMBv1 與網路芳鄰連線異動（舊設備斷線主因）

> [!danger] 為什麼裝完後，舊 NAS、舊 Server 或事務機「掃描到資料夾」會連不上？
> 這次更新動到了底層 **NTLM 驗證協定** 的預設行為，加上 24H2~26H2 原本的 SMB 強制政策，形成「三道封鎖線」。

### 1. 【本次新增】強制預設開啟「NTLM 訊息簽章 (NTLM Message Signing)」
- **變動內容**：自 `KB5132061` 起，Windows 用戶端在非匿名連線時，**預設強制要求提供 NTLM 訊息簽章**（防範 NTLM Relay 攻擊）。
- **衝擊對象**：走 **SMBv1** 的老舊設備（舊 NAS、Win2003/2008、舊版 Samba、事務機 Scan to SMB）皆依賴 NTLM。因老舊韌體無法處理帶有簽章要求的 NTLM 封包，會直接拒絕連線（常見報錯：`指定的網路密碼不正確`、`錯誤碼 58 / 64 / 86`）。

### 2. 【基底政策】24H2 / 25H2 / 26H2 原本的 SMB 預設限制
1. **預設不安裝 SMBv1**：`SMB 1.0/CIFS File Sharing Support` 預設關閉。
2. **強制要求 SMB 數位簽章 (`RequireSecuritySignature`)**：預設強制要求簽章，舊設備未開簽章即斷線。
3. **封鎖不安全的來賓登入 (`InsecureGuestLogons`)**：預設阻擋免帳密（Guest 匿名）共用資料夾。

### 3. 【底層修補】SMB 伺服器驅動程式 (`srvnet.sys`)
- 修補了 `srvnet.sys` 重大 RCE 漏洞（`CVE-2026-113859`，CVSS 7.5）。

### 🛠️ SMBv1 / 舊設備連線失敗急救指令 (PowerShell 管理員)
- **檢查 SMBv1 狀態**：
  ```powershell
  Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol-Client
  ```
- **重新啟用 SMBv1**：
  ```powershell
  Enable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol-Client -NoRestart
  ```
- **關閉 SMB 簽章強制要求**：
  ```powershell
  Set-SmbClientConfiguration -RequireSecuritySignature $false -Force
  ```
- **允許 Guest 免密碼登入**：
  ```powershell
  Set-SmbClientConfiguration -EnableInsecureGuestLogons $true -Force
  ```
- **調整 NTLM 訊息簽章 (GUI)**：執行 `secpol.msc` $\rightarrow$ 本機原則 $\rightarrow$ 安全性選項 $\rightarrow$ 檢查「網路安全性: NTLM SSP 根據的最小工作階段安全性」，取消勾選「需要訊息簽章」。

---

## ⚠️ 二、 KB5132061 已知 5 大災情與環境衝擊

> [!warning] 部署前必讀注意事項
> 1. **商用軟體/ERP 閃退崩潰 (`0xc0000005`)**：傳統商用軟體（如 Sage 50 等會計系統）開啟視窗會直接 Crash，官方建議暫緩更新。
> 2. **安裝卡在 99% 無限循環**：部分主機與 VM 安裝至 99% 失敗回滾（錯誤碼 `0x800f0993`、`0x80070306`、`0x800f0989`），重啟後重複嘗試。
> 3. **藍屏 (BSOD) 與強制開啟 HVCI**：部分回報 `0x9F` 藍屏；更新後會**自動強制開啟「核心隔離 - 記憶體完整性」**，舊硬體驅動或硬體鎖易衝突藍屏。
> 4. **Secure Boot 憑證更新導致多次重啟**：包含 2026 新憑證部署，會自動重開機 2~3 次且短暫黑畫面，**切勿中途斷電**。
> 5. **PowerShell 數位簽章驗證變嚴格**：已簽章的 `.ps1` 腳本若含非標準 UTF-8 字元（如 Big5 中文註解），會驗證失敗拒絕執行，需改存為 **`UTF-8 with BOM`**。

---

## 🔧 三、 本次修復的舊 Bug 與新增功能

- **修復 10 月更新 Bug**：
  - 修復新增 AD 網域印表機跳出 `錯誤碼 87` 的問題。
  - 修復遠端桌面 (RDP) 因憑證不符無法連線的問題。
  - 修復 USB Audio 1.0 外接音效裝置跳 `錯誤碼 10` 無聲的問題。
- **新增功能**：
  - 檔案總管「右鍵選單」支援自訂 (`Customize menu`)。
  - 支援「雲端重建 (Cloud Rebuild)」免隨身碟直接下載映像重灌。

---

## 🧰 四、 實戰維運指令速查（跳過更新關機 / 移除更新）

- **方法 1：無倒數直接斷電關機（不觸發更新）**
  - 指令：`shutdown /p /f`
- **方法 2：停用更新服務後關機 (`/s`) 或重開機 (`/r`)**
  - 指令 1：`net stop wuauserv`
  - 指令 2：`net stop TrustedInstaller`
  - 指令 3：`shutdown /s /f /t 0` (若要重開機則將 `/s` 改為 `/r`)
- **方法 3：GUI 桌面密技跳過更新**
  - 點擊空白桌面 $\rightarrow$ 按 `Alt + F4` (RDP 內按 `Ctrl + Alt + End`) $\rightarrow$ 下拉選單選擇純「關機」或「重新啟動」。
- **強制移除本次 11 月更新 (CMD 管理員)**
  - Win11 指令：`wusa /uninstall /kb:5132061`
  - Server 2022 指令：`wusa /uninstall /kb:5131090`
  - *(移除重開機後，務必至 Windows Update 點選「暫停更新」)*

---

## 📎 附錄：2026年11月同場加映重大資安待辦清單

- [x] **Linux 核心 MPTCP 漏洞 (`CVE-2026-98432`, CVSS 9.8)**
  - 影響 Kernel 4.4+（RHEL 5 / CentOS 6 / RHEL 7 不受影響；vCenter 7.0 與 Debian 10 因核心 4.19 無原生 MPTCP 風險低）。
  - 檢查指令：`ss -M -lpn`
  - 緩解鎖死：`sudo sysctl -w net.mptcp.enabled=0` 並寫入 `/etc/sysctl.d/90-disable-mptcp.conf`
- [ ] **PHP2Shell 重大漏洞 (`CVE-2026-109618` / `109619`)**
  - **Linux 主機 (OCSNG 等)**：執行 `sudo dnf update "php*" -y` 並重啟 `httpd php-fpm`。
  - **趨勢科技 Apex One / Central**：先升至前置版（Apex One `B18012+` 或 `B20045`；Apex Central `B7328`），再套用 PHP 手動修補包或等待 11/11 後之繁中 Critical Patch。
- [ ] **Synology NAS 安全更新 (`SA-26:14` ~ `26:19`)**
  - 升級 DSM 至最新（防範 `SA-26:19` 免登入 SQLi 與 rsync 漏洞）。
  - 套件中心全面更新（修補 PHP2Shell、Node2Shell、Plex2Shell），並**移除已 EOL 的 PHP 7.3**。
- [ ] **PolarEdge 殭屍網路 IoC 封鎖與清查**
  - 防火牆檢查並封鎖惡意 IP：`23.249.20.65`、`47.80.241.89` (Port `48080`, `/get/curl_arm32`)。
