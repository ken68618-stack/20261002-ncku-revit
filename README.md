# NCKU Revit × MCP 實作紀錄

本專案用於整理 Revit 與 MCP（Model Context Protocol）的學習與實作紀錄，包含環境設定、AI 輔助操作流程，以及後續累積的範例與成果。

- **紀錄日期**：2026 年 10 月 2 日
- **實作環境**：Windows、Autodesk Revit 2026、Codex
- **Revit MCP 上游專案**：[shuotao/REVIT_MCP_study](https://github.com/shuotao/REVIT_MCP_study)

## 專案目標

- 建立 Revit 與 AI 助手之間的 MCP 連線。
- 透過自然語言查詢模型資訊，逐步練習 AI 輔助 BIM 操作。
- 保留可重現的操作步驟、畫面與問題排除紀錄。

## 運作方式

```text
Codex
  │ MCP / stdio
  ▼
Revit MCP Server（Node.js）
  │ WebSocket：localhost:8964
  ▼
Revit MCP 外掛
  │ Revit API
  ▼
Autodesk Revit 2026
```

AI 透過 MCP Server 呼叫工具，再由 Revit 外掛執行對應的模型操作。Revit 外掛與 MCP Server 都需要準備完成。

## 環境需求

| 項目 | 說明 |
| --- | --- |
| 作業系統 | Windows 10 或更新版本 |
| Revit | 本次實作使用 Revit 2026 |
| AI 助手 | Codex，並設定本機 MCP Server |
| Node.js | 20 以上，建議使用 LTS 版本 |
| Revit MCP 外掛 | 安裝至對應的 Revit 年份 |
| .NET SDK | 自行編譯外掛時需要，版本依上游專案的編譯需求準備 |

## 安裝與設定

本儲存庫提供實作說明；Revit 外掛與 MCP Server 的程式碼、安裝工具由上游專案提供。

1. 下載 [REVIT_MCP_study](https://github.com/shuotao/REVIT_MCP_study)，並解壓縮到固定位置。
2. 關閉 Revit，執行上游專案的 `scripts/setup.bat`，選擇 Revit 2026。
3. 在 Codex 的 MCP 設定中加入 Revit MCP Server；若安裝程式沒有設定 Codex，可參考下方範例。
4. 重新開啟 Revit，依安全提示自行決定是否載入 **Revit MCP Plugin**。
5. 開啟模型，在 **MCP Tools** 面板啟用 **MCP 服務（開／關）**。
6. 重新啟動 Codex，測試能否讀取目前模型資訊。

完整安裝流程請參考[上游繁體中文說明](https://github.com/shuotao/REVIT_MCP_study/blob/main/README.zh-TW.md)。

### Codex 設定範例

在 Codex 的 `config.toml` 加入以下設定，將路徑改成實際的上游專案位置。`node` 必須可在命令列執行；也可改成 `node.exe` 的完整路徑。

```toml
[mcp_servers.revit-mcp]
command = "node"
args = ['C:\path\to\REVIT_MCP_study\MCP-Server\build\index.js']
startup_timeout_sec = 30
tool_timeout_sec = 180
enabled = true

[mcp_servers.revit-mcp.env]
REVIT_MCP_HOST = "localhost"
REVIT_MCP_PORT = "8964"
MCP_PROFILE = "full"
```

若已完成設定，無需重複新增同名區段。設定方式可參考 [Codex MCP 文件](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)。

## 開始使用

每次使用時，先開啟 Revit 與模型，確認 MCP 服務已啟用，再向 Codex 提出需求。

可先從讀取資訊的操作開始：

```text
請取得目前開啟的 Revit 專案基本資訊。
請列出目前模型的所有樓層。
請統計目前模型中的門與窗數量。
```

需要修改模型時，先儲存備份，並清楚指定操作範圍及預期結果。

## 實作紀錄方式

後續加入範例時，建議一併記錄：

- **目標**：這次要查詢或完成什麼工作。
- **環境**：Revit 版本、MCP 版本與使用的測試模型。
- **操作**：提示詞、執行步驟與必要參數。
- **結果**：操作前後畫面、產出內容與人工檢查結果。
- **問題**：錯誤訊息、處理方式與尚未解決的限制。

## 常見問題

| 狀況 | 檢查方式 |
| --- | --- |
| Revit 看不到 MCP Tools | 確認外掛裝在正確年份的 Addins 目錄，並重新啟動 Revit |
| Codex 找不到 Revit 工具 | 確認 MCP 設定、Node.js 執行路徑與 `build/index.js` 是否存在，再重新啟動 Codex |
| 工具無法連接 Revit | 確認 Revit 已開啟、模型已載入、MCP 服務已啟用，且兩端使用相同連接埠 |
| 切換 AI 軟體後無法連線 | 在 Revit 點選「切換／釋放連線」，再連接新的 AI 軟體；同一時間只使用一個連線 |

## 參考資料

- [Revit MCP 上游專案](https://github.com/shuotao/REVIT_MCP_study)
- [Revit MCP 繁體中文說明](https://github.com/shuotao/REVIT_MCP_study/blob/main/README.zh-TW.md)
- [Codex MCP 設定文件](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)

Autodesk Revit 為 Autodesk 的產品。本專案為學習與實作紀錄；使用上游程式碼時，請遵循其授權條款。
