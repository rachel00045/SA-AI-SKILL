# SA-AI-SKILL

適合 SA 與 PG 的 Codex Skill：由 ChatGPT 透過唯讀 MCP 負責需求理解、方案規劃與實作 Review，再由 Codex 在本機執行程式修改、測試與驗證。

## 使用方式

### 驗收 TFS 議題

安裝 Skill 後，在 Codex 開啟該議題所屬的專案資料夾，並在專案聊天直接貼上 TFS 工項連結。把下列範例換成你的工項連結即可。

```text
https://tfs.example.com/DefaultCollection/專案名稱/_workitems/edit/12345
```

1. Codex 會讀取工項與驗收條件，從專案中找出對應的 UC（使用案例）與版本。
2. Codex 會在指定的測試環境執行案例，依預期結果、實際結果與佐證判定通過、失敗、阻塞或未執行。
3. 正式截圖與驗收紀錄會存入該專案既有的 `03系統測試` 資料夾，完成後會回報結果和檔案路徑。
4. 遇到規格衝突或缺少環境、權限、測試資料時，Codex 會暫停受影響的案例並提出具體問題。其他可驗證的案例會繼續執行，收到回覆後會沿用原紀錄續測。

### 分析需求與實作

安裝後可使用下列指令開始：

```text
使用 SA-AI-SKILL，先分析這個需求，再規劃並實作。
```

本 Skill 會依任務需要串接 `sana` 與 `sa-knowledge-base`，保留需求、決策、UC、測試、TFS 草稿與程式變更之間的追溯關係。

## 安全原則

- ChatGPT 只透過 MCP 讀取必要資料，沒有寫檔或執行 shell 權限。
- 第一次連線前會告知程式碼、diff 與測試摘要可能傳送至 ChatGPT。
- 不會自動建立 public tunnel、修改 Codex 設定、安裝套件、更新 Skill 或刪除 connector。
- 不會自動開立 TFS、部署、push、修改正式資料或執行不可逆 migration。
- 這是一個非官方社群整合流程；使用前請先檢查 workspace 的資料分類與連線權限。

完整流程請見 [skill/SKILL.md](skill/SKILL.md)。測試情境請見 [skill/evals/evals.json](skill/evals/evals.json)。
