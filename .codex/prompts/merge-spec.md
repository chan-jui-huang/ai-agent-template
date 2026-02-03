---
description: Merge specs into a single cohesive SpecKit doc set
argument-hint: [FROM=<path>] [TO=<path>] [OUT=<path>] [MERGED_FROM="<note>"]
---

請把 $FROM 到 $TO 依序合併到 $OUT（不存在就建立）。

合併後文件必須看起來像「一次到位」的 SpecKit 文件，而不是拼貼或補丁。

$OUT 內所有文件都要重整，並依序吸收內容（保持語意但整合成單一版本）。

保留 refactor 的語意，如果refactor的部分有對應的主 User Story，請整合回去，不要留下獨立補強段落。如果refactor沒有對應的User Story，就維持現狀。

所有內容需刪除重複情境或重複敘述，保留必要的核心流程與錯誤情境。

Contracts 檔需完整合併所有 endpoints/schema，並對齊規格中的參數限制、欄位命名、型別與必填規則。

Plan 需保持整合後的結構（例如 Phase/步驟），但 Interfaces 區塊必須詳細到可直接實作，並明確交代錯誤對應、路由/掛載位置、資料一致性或回滾策略、關鍵輸入輸出欄位規則。

Tasks 一律不處理：不要建立、不要更新、不要合併。

若提供 $MERGED_FROM，請在文件開頭加一段「Merged From」註記（只出現一次）。

最後請回報合併結果與你如何合併。