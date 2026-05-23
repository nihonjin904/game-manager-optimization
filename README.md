# 🎮 Windows Game Process Suspender & Memory/VRAM Optimizer (電腦與遊戲休眠優化器)

這是一套專門解決在 Windows 環境下，高負載遊戲（如《鳴潮》、《原神》等）佔用大量記憶體 (RAM) 與顯示卡記憶體 (VRAM)，導致背景開發、多工處理或**遠端桌面 (RDP) 連線極度卡頓**問題的自動化優化工具。

---

## 🌟 核心特色 (Core Features)

1. ⚡ **智慧 Idle 偵測 (Smart Focus Suspender)**
   * 自動監控實體記憶體佔用大於 **1GB** 的高負載進程（自動鎖定遊戲）。
   * 當檢測到遊戲失去焦點（切換至背景）且處於閒置狀態時，將其優先級調降至 `IDLE_PRIORITY_CLASS`，減少 CPU 資源競爭。

2. 📉 **記憶體物理空降 (Empty Working Set RAM/VRAM Clean)**
   * 調用 Windows API `psapi.EmptyWorkingSet`，強制將休眠遊戲佔用的實體記憶體分頁寫入虛擬分頁檔，**瞬間釋放高達 80% 的實體 RAM 與部分 VRAM**，讓前台開發工具（如 IDE、編譯器）與 RDP 獲得 100% 的流暢度。

3. 🔄 **零感恢復 (Auto Resume)**
   * 當用戶重新切換回遊戲前台時，系統自動偵測並在一瞬間將優先級恢復為正常，遊戲無縫接軌不卡頓。

4. 🔔 **穿透專注助手通知 (Toast Notification Alarm)**
   * 調用 Windows 原生 API 發送精美 Toast 系統通知，支援遊戲全螢幕專注助手（Focus Assist）穿透，確保在休眠或回復時能收到即時氣泡提示。

5. 🎮 **多開遊戲背景限幀與防崩潰 (Multi-Game FPS Limiter & DX12 Bypass)**
   * **背景 10 FPS 限幀**：自動掃描虛幻引擎的 `GameUserSettings.ini` 配置，在遊戲失去焦點時強制鎖定背景 10 FPS，使 GPU 佔用降低 90%，前台遊戲獨佔顯卡算力。
   * **DX12 防崩潰守護**：自動避開傳統 `NtSuspendProcess` 掛起，改以調降優先權至 `IDLE` (CPU 降頻) 方式運行，防止 DX12 遊戲渲染超時閃退 (`DXGI_ERROR_DEVICE_REMOVED`)。
   * **防置換掉幀卡頓**：智慧停用背景遊戲的頻繁 `EmptyWorkingSet`，避免 Page Fault 觸發 Page Swapping I/O 卡頓，保障切換無縫流暢。

---

## 🛠️ 資源優化與調度架構 (Architecture & Resource Flow)

以下為多開遊戲休眠、背景限幀以及防崩潰設計的資源流轉與調度流程：

```mermaid
flowchart TD
    subgraph 前台活動狀態 ["前台活動遊戲 (如《鳴潮》)"]
        A1["獨佔 100% GPU / CPU 核心算力"]
        A2["運行優先級: ABOVE_NORMAL"]
    end

    subgraph 背景休眠優化 ["背景遊戲休眠與優化 (如《終末地》、《洛克王國》)"]
        B1["1. 智慧識別與監控焦點切換"]
        B2["2. 自動限制背景 FPS = 10 (配置注入)"]
        B3["3. 調降優先級至 IDLE (CPU 降頻)"]
        B4["4. DX12 崩潰防護 (繞過 NtSuspend, 避免 DXGI 閃退)"]
        B5["5. 顯存 VRAM 自動置換到 RAM (GPU 佔用暴降 90%)"]
        B6["6. 防 Page Fault 卡頓設計 (禁用頻繁 EmptyWorkingSet)"]
    end

    FocusChange{"用戶切換視窗"}
    FocusChange -->|切回前台| A1
    FocusChange -->|切到背景| B1
    
    B1 --> B2 --> B3 --> B4 --> B5 --> B6
```

---


## 🔒 檔案安全與下載分享說明 (Security & Distribution)

為保護個人開發原始碼，本專案僅提供設計思維展示，核心運行腳本不直接公開。

### 📥 朋友專屬下載通道
如果你是受邀的朋友，可以直接使用以下連結下載加密的壓縮包：

👉 **[一鍵直達下載 (game_optimization_release.zip)](https://github.com/nihonjin904/game-manager-optimization/raw/master/game_optimization_release.zip)**

*   **解壓密碼**：請聯繫作者私下獲取（請勿公開傳播）。
*   **使用方法**：解壓後修改 `config.json` 設定遊戲進程，執行對應的批次檔即可常駐背景運行！
