# SA-AI-SKILL

適合 SA 與 PG 的 Skill，可在 Codex 或 Claude 按專案執行需求分析、UC、議題草稿與驗收。Codex 可按需搭配 ChatGPT 做唯讀第二審。

## 安裝與環境

下載本 repo，取出完整的 [`sa-ai-skill` 資料夾](sa-ai-skill)。資料夾名稱與 `SKILL.md` 的 `name` 都是 `sa-ai-skill`。

| 平台 | 安裝方式 |
| --- | --- |
| Codex | 將資料夾放到專案的 `.agents/skills/`，或放到使用者的 `~/.agents/skills/` 供各專案使用。到目標專案開啟聊天，從 Skill 選單選用 `sa-ai-skill`，或使用下方範例。 |
| Claude Code | 將資料夾放到專案的 `.claude/skills/`，或使用者的 `~/.claude/skills/`。在目標專案執行 `/sa-ai-skill`。 |
| Claude 網頁版 | 將 `sa-ai-skill` 資料夾單獨壓成 ZIP，確認第一層是 `sa-ai-skill/`，再到 Customize → Skills 上傳並啟用。 |
| 其他 AI | 支援 Agent Skills 的工具可依其規則安裝此資料夾。一般聊天可提供 `SKILL.md` 和授權使用的資料，讓 AI 依流程產出草稿或驗收計畫。 |

官方說明：[Codex Skills](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills)、[Claude Code Skills](https://code.claude.com/docs/en/skills#choose-where-skills-load)、[Claude 自訂 Skill 上傳](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills)。

### 按功能另行準備

輔助 Skill 請向團隊取得並安裝到同一個 AI 環境。公司 TFS 連接器需在該環境另行設定，並確認使用者有相應專案權限。若 TFS 或測試站限內網存取，執行環境也需要能連到該站點。

| 功能 | 輔助 Skill | 連接器與專案環境 |
| --- | --- | --- |
| 需求分析、UC、規格與測試追溯 | 已內建 [Sana 流程](sa-ai-skill/references/sana-workflow.md)，無需另裝 Skill。 | 提供需求來源、對應 UC 與專案檔案。若需修改程式及跑測試，執行環境也要有專案要求的工具與相依套件。 |
| 整理 TFS 議題草稿 | `tfs-issue-opening` 提供完整開單格式與專案範本。缺少時仍可依本 Skill 整理文字草稿。 | 要從連結查工項、父代、同類範本或重複項目，需唯讀 TFS 介面。若只能離線整理，請提供可分享的工項資料。 |
| 正式建立或更新 TFS 工項 | 新建用 `tfs-create-workitem`，拆分多張相依工項用 `tfs-work-item-breakdown`，更新既有工項用 `tfs-update-workitem`。 | 需要可讀寫的 TFS 連接器與對應權限。[開立流程](sa-ai-skill/SKILL.md#整理與開立-tfs-議題)說明草稿、授權與建立後回讀。 |
| 貼 TFS 連結並執行驗收 | 驗收流程已包含在本 Skill。 | 需要能讀工項的 TFS 介面、能操作並截圖的瀏覽器或測試工具、測試環境與有權限的測試帳號。執行環境須能讀取專案 UC，並寫入該專案既有的 `03系統測試`。 |
| ChatGPT 第二審與 Codex 實作協作 | 使用本 Skill 的 C2C 流程。 | 另行配置經審核與授權的唯讀 `codex-with-chatgpt` MCP bridge，以及相容的 Codex 內建瀏覽器。此設定只在啟用 C2C 時需要。 |

瀏覽器、本機檔案與內網能力視 AI 平台和工作階段而定。可載入 Skill 後，先確認它能在目標專案讀取 UC 和 TFS 工項，再執行驗收。


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

### 整理或開立 TFS 議題

在專案聊天提供需求、問題、案件編號或驗收結果，可用下列指令。

```text
使用 SA-AI-SKILL，依這個專案的既有格式，幫我整理一張 TFS 議題草稿：[填入需求、問題或驗收結果]
```

Codex 會查找對應的父工項與同類議題，檢查是否重複，再提出標題、描述、可驗收的預期結果和工項設定。優先沿用專案格式。找不到範本時，採「說明、路徑、需求、預期結果」，描述預設使用一般字重的縮排清單。

開單會使用 `tfs-issue-opening` 整理內容，並在確認草稿且明確授權後，由 `tfs-create-workitem` 建立及回讀工項。要加「版更測試機」標記時，請在當次明確指定。

### 分析需求與實作

安裝後可使用下列指令開始：

```text
使用 SA-AI-SKILL，先分析這個需求，再規劃並實作。
```

本 Skill 使用內建的 Sana 流程，保留需求、決策、UC、測試、TFS 草稿與程式變更之間的追溯關係。

## 安全原則

- 選用 ChatGPT 第二審時，ChatGPT 只透過 MCP 讀取必要資料，沒有寫檔或執行 shell 權限。
- 啟用第二審並第一次連線前會告知程式碼、diff 與測試摘要可能傳送至 ChatGPT。
- 不會自動建立 public tunnel、修改 Codex 設定、安裝套件、更新 Skill 或刪除 connector。
- 不會自動開立 TFS、部署、push、修改正式資料或執行不可逆 migration。
- 這是一個非官方社群整合流程；使用前請先檢查 workspace 的資料分類與連線權限。

完整流程請見 [sa-ai-skill/SKILL.md](sa-ai-skill/SKILL.md)。測試情境請見 [sa-ai-skill/evals/evals.json](sa-ai-skill/evals/evals.json)。
