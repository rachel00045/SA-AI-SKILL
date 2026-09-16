# SA／PG ChatGPT-Codex

適合 SA 與 PG 的 Codex Skill：由 ChatGPT 透過唯讀 MCP 負責需求理解、方案規劃與實作 Review，再由 Codex 在本機執行程式修改、測試與驗證。

## 使用方式

安裝後可使用下列指令開始：

```text
使用 SA／PG ChatGPT-Codex，先分析這個需求，再規劃並實作。
```

本 Skill 會依任務需要串接 `sana` 與 `sa-knowledge-base`，保留需求、決策、UC、測試、TFS 草稿與程式變更之間的追溯關係。

## 安全原則

- ChatGPT 只透過 MCP 讀取必要資料，沒有寫檔或執行 shell 權限。
- 第一次連線前會告知程式碼、diff 與測試摘要可能傳送至 ChatGPT。
- 不會自動建立 public tunnel、修改 Codex 設定、安裝套件、更新 Skill 或刪除 connector。
- 不會自動開立 TFS、部署、push、修改正式資料或執行不可逆 migration。
- 這是一個非官方社群整合流程；使用前請先檢查 workspace 的資料分類與連線權限。

完整流程請見 [skill/SKILL.md](skill/SKILL.md)。測試情境請見 [skill/evals/evals.json](skill/evals/evals.json)。
